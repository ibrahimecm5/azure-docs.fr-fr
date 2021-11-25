---
title: Monter un système de fichiers virtuel sur un pool
description: Découvrez comment monter un système de fichiers virtuel sur un pool Batch.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 11/11/2021
ms.openlocfilehash: ed83f5771a451f92c69ba5f80de7bfa7d8388778
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369414"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Monter un système de fichiers virtuel sur un pool Batch

Azure Batch prend en charge le montage de stockage cloud ou d’un système de fichiers externe sur les nœuds de calcul Windows ou Linux dans vos pools Batch. Lorsqu’un nœud de calcul rejoint un pool, le système de fichiers virtuel est monté et traité comme un lecteur local sur ce nœud. 

Vous pouvez monter des systèmes de fichiers tels que : 

- Azure Files
- Stockage Blob Azure
- NFS (Network File System) incluant un [cache Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)
- Common Internet File System (CIFS)

Dans cet article, vous allez apprendre à monter un système de fichiers virtuel sur un pool de nœuds de calcul à l’aide de la [Bibliothèque de gestion Batch pour .NET](/dotnet/api/overview/azure/batch).

> [!NOTE]
> Le montage d’un système de fichiers virtuel est pris en charge sur les pools Batch créés le 8 août 2019 ou après. Les pools Batch créés avant cette date ne prennent pas en charge cette fonctionnalité.

## <a name="benefits-of-mounting-on-a-pool"></a>Avantages du montage sur un pool

Le montage du système de fichiers sur le pool, au lieu de laisser les tâches récupérer leurs propres données à partir d’un jeu de données volumineux, facilite et rend plus efficace l’accès aux données nécessaires pour les tâches.

Imaginez un scénario avec plusieurs tâches nécessitant l’accès à un jeu de données commun, comme le rendu d’un film. Chaque tâche restitue un ou plusieurs frames à la fois à partir des fichiers de scène. En montant un lecteur contenant les fichiers de scène, il est plus facile pour les nœuds de calcul d’accéder aux données partagées.

En outre, le système de fichiers sous-jacent peut être choisi et mis à l’échelle indépendamment en fonction des performances et de l’échelle (débit et IOPS) requises par le nombre de nœuds de calcul qui accèdent simultanément aux données. Par exemple, il est possible d’utiliser un cache en mémoire distribué [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md), qui peut être utilisé pour prendre en charge des rendus d’images à grande échelle avec des milliers de nœuds de rendu simultanés, en accédant aux données source qui résident localement. Au lieu de cela, pour les données qui se trouvent déjà dans le stockage d’objets blob sur le cloud, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) peut être utilisé pour monter ces données en tant que système de fichiers local. Blobfuse est disponible uniquement sur les nœuds Linux même si [Azure Files](../storage/files/storage-files-introduction.md) fournit un workflow similaire et est disponible sur Windows et Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Monter un système de fichiers virtuel sur un pool  

Le montage d’un système de fichiers virtuel sur un pool rend le système de fichiers disponible pour chaque nœud de calcul du pool. La configuration du système de fichiers se produit lorsqu’un nœud de calcul rejoint un pool, redémarre ou est réinitialisé.

Pour monter un système de fichiers sur un pool, créez un objet `MountConfiguration`. Choisissez l’objet qui correspond à votre système de fichiers virtuel : `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration` ou `CifsMountConfiguration`.

Tous les objets de configuration de montage ont besoin des paramètres de base suivants. Certaines configurations de montage ont des paramètres spécifiques au système de fichiers utilisé, ce que nous évoquons plus en détail dans les exemples de code.

- **Nom ou source du compte** : Pour monter un partage de fichiers virtuel, vous avez besoin du nom du compte de stockage ou de sa source.
- **Chemin de montage relatif ou source** : l’emplacement du système de fichiers monté sur le nœud de calcul, par rapport au répertoire `fsmounts` standard accessible sur le nœud via `AZ_BATCH_NODE_MOUNTS_DIR`. L’emplacement exact varie selon le système d’exploitation utilisé sur le nœud. Par exemple, l’emplacement physique sur un nœud Ubuntu est mappé avec `mnt\batch\tasks\fsmounts`. Sur un nœud CentOS, l’emplacement est mappé à `mnt\resources\batch\tasks\fsmounts`.
- **Options de montage ou options blobfuse** : Ces options décrivent les paramètres spécifiques pour le montage d’un système de fichiers.

Une fois l’objet `MountConfiguration` créé, affectez l’objet à la propriété `MountConfigurationList` lorsque vous créez le pool. Le montage du système de fichiers se produit lorsqu’un nœud rejoint un pool, redémarre ou est réinitialisé.

Lorsque le système de fichiers est monté, une variable d’environnement `AZ_BATCH_NODE_MOUNTS_DIR` est créée ; elle pointe vers l’emplacement des systèmes de fichiers montés et des fichiers journaux, qui sont utiles pour la résolution des problèmes et le débogage. Les fichiers journaux sont expliqués plus en détail dans la section [Diagnostiquer les erreurs de montage](#diagnose-mount-errors).  

> [!IMPORTANT]
> Le nombre maximal de systèmes de fichiers montés sur un pool est de 10. Consultez [Quotas et limites du service Batch](batch-quota-limit.md#other-limits) pour plus d’informations et pour connaître les autres limitations.

## <a name="mount-azure-file-share-with-powershell"></a>Monter un partage de fichiers Azure avec PowerShell

Vous pouvez monter un partage de fichiers Azure sur un pool Batch à l’aide d’[Azure PowerShell](/powershell/) ou d’[Azure Cloud Shell](../cloud-shell/overview.md).

# <a name="windows"></a>[Windows](#tab/windows)

1. Connectez-vous à votre abonnement Azure.

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Obtenez le contexte de votre compte Batch.
    
    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. Créez un pool Batch avec les paramètres suivants. Remplacez les valeurs d’exemple par vos propres informations en fonction des besoins.

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "Storage-Account-key")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
    ```

1. Accédez aux fichiers de montage à l’aide du chemin d’accès direct de votre lecteur. Par exemple :

    ```powershell
    cmd /c "more S:\folder1\out.txt & timeout /t 90 > NULL"
    ```

1. Vérifiez que le fichier de sortie est correct.

1. Si vous utilisez le protocole RDP (Remote Desktop Protocol) (RDP) ou le protocole SSH, ajoutez des informations d’identification pour accéder directement au lecteur `S`. L’agent Azure Batch n’accorde l’accès que pour les tâches Azure Batch dans Windows. Lorsque vous vous connectez au nœud via le protocole RDP, votre compte d’utilisateur ne dispose pas d’un accès automatique au lecteur de montage. 

    Utilisez `cmdkey` pour ajouter vos informations d’identification. Remplacez les valeurs d’exemple par vos propres informations.

    ```powershell
    cmdkey /add:"<storage-account-name>.file.core.windows.net" /user:"Azure\<storage-account-name>" /pass:"<storage-account-key>"
    ```

# <a name="linux"></a>[Linux](#tab/linux)

1. Connectez-vous à votre abonnement Azure.

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Obtenez le contexte de votre compte Batch.

    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. Créez un pool Batch avec les paramètres suivants. Remplacez les valeurs d’exemple par vos propres informations en fonction des besoins.

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "<Storage-Account-key>", "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
     
    ```

1. Accédez aux fichiers de montage à l’aide de la variable d’environnement `AZ_BATCH_NODE_MOUNTS_DIR`. Par exemple :

    ```bash
    /bin/bash -c 'more $AZ_BATCH_NODE_MOUNTS_DIR/S/folder1/out.txt; sleep 20s'
    ```

    Si vous le souhaitez, vous pouvez également accéder aux fichiers de montage à l’aide du chemin d’accès direct.

1. Vérifiez que le fichier de sortie est correct.

1. Si vous utilisez le protocole RDP ou SSH, vous pouvez accéder manuellement au lecteur `S` directement. Utilisez le chemin d’accès `/mnt/batch/tasks/fsmounts/S`.

---

### <a name="troubleshoot-powershell-mounting"></a>Résoudre les problèmes de montage PowerShell

Lorsque vous montez un partage de fichiers Azure dans un pool Batch avec PowerShell ou Cloud Shell, vous pouvez recevoir l’erreur suivante :

```text
Mount Configuration Error | An error was encountered while configuring specified mount(s)
Message: System error (out of memory, cannot fork, no more loop devices)
MountConfigurationPath: S
```

Si vous recevez cette erreur, RDP ou SSH vers le nœud pour vérifier les fichiers journaux associés. L’agent Batch implémente le montage différemment sur Windows et Linux. Sur Linux, Batch installe le package `cifs-utils`. Ensuite, Batch émet la commande mount. Sur Windows, Batch utilise `cmdkey` pour ajouter les informations d’identification de votre compte Batch. Ensuite, Batch émet la commande de montage via `net use`. Par exemple :

```powershell
net use S: \\<storage-account-name>.file.core.windows.net\<fileshare> /u:AZURE\<storage-account-name> <storage-account-key>
```

# <a name="windows"></a>[Windows](#tab/windows)

1. Connectez-vous au nœud via RDP.

1. Ouvrez le fichier journal, `fshare-S.log`. Le chemin d’accès du fichier est `D:\batch\tasks\fsmounts`.

1. Passez en revue les messages d’erreur. Par exemple :

    ```text
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. Résolvez le problème en suivant les consignes de [Résoudre les problèmes liés à Azure Files dans Windows Server Message Block (SMB)](../storage/files/storage-troubleshoot-windows-file-connection-problems.md).

# <a name="linux"></a>[Linux](#tab/linux)

1. Connectez-vous au nœud via SSH.

1. Ouvrez le fichier journal, `fshare-S.log`. Le chemin d’accès du fichier est `/mnt/batch/tasks/fsmounts`.

1. Passez en revue les messages d’erreur. Par exemple : `mount error(13): Permission denied`.

1. Résolvez le problème à l’aide ds consignes de [Résoudre les problèmes liés à Azure Files dans Linux (SMB)](../storage/files/storage-troubleshoot-linux-file-connection-problems.md).

---

Si vous ne pouvez pas utiliser RDP ou SSH pour vérifier les fichiers journaux sur le nœud, vérifiez directement les journaux Batch. Utilisez cette méthode pour les journaux Windows et Linux.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la barre de recherche, entrez et sélectionnez **Comptes Batch**.

1. Dans la page **Comptes Batch**, sélectionnez le compte avec votre pool Batch.

1. Dans le menu de la page du compte Batch, sous **Fonctionnalités**, sélectionnez **Pools**.

1. Sélectionnez le nom du pool.

1. Dans le menu de la page du pool Batch, sous **général**, sélectionnez **Nœuds**.

1. Sélectionnez le nom du nœud.

1. Dans la page **Vue d’ensemble** du nœud, sélectionnez **Charger les journaux Batch**.

1. Dans le volet **Upload batch logs** (Charger les journaux par lots), sélectionnez votre conteneur Stockage Azure. Ensuite, sélectionnez **Choisir un conteneur de stockage**.

1. Sélectionnez et téléchargez les fichiers journaux à partir du conteneur de stockage. 

1. Ouvrez `agent-debug.log`.

1. Passez en revue les messages d’erreur. Par exemple : 

    ```text
    ..20210322T113107.448Z.00000000-0000-0000-0000-000000000000.ERROR.agent.mount.filesystems.basefilesystem.basefilesystem.py.run_cmd_persist_output_async.59.2912.MainThread.3580.Mount command failed with exit code: 2, output:
    
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. Résolvez le problème à l’aide des consignes de [Résoudre les problèmes liés à Azure Files sous Windows](../storage/files/storage-troubleshoot-windows-file-connection-problems.md) ou de [Résoudre les problèmes liés à Azure Files sous Linux](../storage/files/storage-troubleshoot-linux-file-connection-problems.md).

Si vous ne parvenez toujours pas à trouver la cause de l’échec, vous pouvez [monter le partage de fichiers manuellement avec PowerShell](#manually-mount-file-share-with-powershell) à la place.

### <a name="manually-mount-file-share-with-powershell"></a>Montage manuel du partage de fichiers avec PowerShell

Si vous ne parvenez pas à diagnostiquer ou corriger les erreurs de montage avec PowerShell, vous pouvez monter le partage de fichiers manuellement.

# <a name="windows"></a>[Windows](#tab/windows)

1. Créez un pool sans configuration de montage. Par exemple :

    ```powershell
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1  -BatchContext $Context
    ```

1. Attendez que le nœud soit à l’état **Inactif**.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la barre de recherche, entrez et sélectionnez **Comptes de stockage**.

1. Sélectionnez le nom du compte de stockage avec votre partage de fichiers.

1. Dans le menu de la page du compte de stockage, sous **Stockage de données**, sélectionnez **Partages de fichiers**.

1. Dans la page **Partages de fichiers**, sélectionnez le nom du partage de fichiers.

1. Dans la page **Vue d’ensemble** du partage de fichiers, sélectionnez **Se connecter**.

1. Dans le volet **Connecter**, sélectionnez l’onglet **Windows**.

1. Dans **Lettre de lecteur**, entrez le lecteur que vous souhaitez utiliser. Par défaut, il s’agit de `Z`.

1. Dans **Méthode d’authentification**, sélectionnez la façon dont vous souhaitez vous connecter au partage de fichiers.

1. Copiez la commande PowerShell pour le montage du partage de fichiers.

1. Connectez-vous au nœud via RDP.

1. Exécutez la commande que vous avez copiée pour monter le partage de fichiers.

1. Notez les messages d’erreur dans la sortie. Utilisez ces informations pour résoudre les problèmes liés à la mise en réseau.

# <a name="linux"></a>[Linux](#tab/linux)


1. Créez un pool sans configuration de montage. Par exemple :

    ```bash
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -BatchContext $Context
    ```

1. Attendez que le nœud soit à l’état **Inactif**.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la barre de recherche, entrez et sélectionnez **Comptes de stockage**.

1. Sélectionnez le nom du compte de stockage avec votre partage de fichiers.

1. Dans le menu de la page du compte de stockage, sous **Stockage de données**, sélectionnez **Partages de fichiers**.

1. Dans la page **Partages de fichiers**, sélectionnez le nom du partage de fichiers.

1. Dans la page **Vue d’ensemble** du partage de fichiers, sélectionnez **Se connecter**.

1. Dans le volet **Connecter**, sélectionnez l’onglet **Linux**.

1. Entrez le **point de montage** que vous souhaitez utiliser.

1. Copiez la commande Linux pour le montage du partage de fichiers.

1. Connectez-vous au nœud via SSH.

1. Exécutez la commande que vous avez copiée pour monter le partage de fichiers.

1. Notez les messages d’erreur dans la sortie. Utilisez ces informations pour résoudre les problèmes liés à la mise en réseau.


---

## <a name="examples"></a>Exemples

Les exemples de code suivants illustrent le montage de différents partages de fichiers sur un pool de nœuds de calcul.

### <a name="azure-files-share"></a>Partage Azure Files

Azure Files est l’offre standard de systèmes de fichiers cloud d’Azure. Pour plus d’informations sur les paramètres de l’exemple de code, consultez [Utiliser un partage Azure Files - SMB](../storage/files/storage-how-to-use-files-windows.md) ou [Utiliser un partage Azure Files - NFS](../storage/files/storage-files-how-to-create-nfs-shares.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-container"></a>Conteneur de blobs Azure

Une autre option consiste à utiliser le stockage d'objets blob Azure via [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Le montage d’un système de fichiers blob nécessite une `AccountKey`, une `SasKey` ou une `Managed Identity` avec l’accès à votre compte de stockage.

Pour plus d’informations sur l’obtention de ces clés, consultez : 

- [Gestion des clés d’accès au compte de stockage](../storage/common/storage-account-keys-manage.md)
- [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](../storage/common/storage-sas-overview.md)
- [Configurez des identités managées dans des pools Batch](managed-identity-pools.md). 

Pour plus d’informations et des conseils sur l’utilisation de blobfuse, consultez le [projet blobfuse](https://github.com/Azure/azure-storage-fuse).

Pour accéder par défaut au répertoire monté avec blobfuse, exécutez la tâche en tant **qu'administrateur**. Blobfuse monte le répertoire au niveau de l’espace utilisateur et, lors de la création du pool, il est monté en tant que racine. Dans Linux, toutes les tâches de l’**Administrateur** sont de type racine. Toutes les options du module FUSE sont décrites dans la [Page de référence FUSE](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Pour plus d’informations et de conseils sur l’utilisation de blobfuse, consultez la [FAQ sur la résolution des problèmes](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ). Vous pouvez également consulter les [Problèmes GitHub dans le référentiel blobfuse](https://github.com/Azure/azure-storage-fuse/issues) pour vérifier les problèmes actuels de blobfuse et leur résolution.

> [!NOTE]
> L’exemple ci-dessous montre `AccountKey`, `SasKey` et `IdentityReference`, mais ils s’excluent mutuellement ; un seul peut être spécifié.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "SasKey",
                IdentityReference = new ComputeNodeIdentityReference("/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name"),
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

> [!TIP]
>Si vous utilisez une identité managée, assurez-vous que l’identité a été [attribuée au pool](managed-identity-pools.md) pour qu’elle soit disponible sur la machine virtuelle qui effectue le montage. L’identité doit avoir le rôle `Storage Blob Data Contributor` pour fonctionner correctement.

### <a name="network-file-system"></a>Système de gestion de fichiers en réseau

Les systèmes de fichiers réseau (NFS) peuvent être montés sur des nœuds de pool, ce qui permet à Azure Batch d’accéder facilement aux systèmes de fichiers traditionnels. Il peut s’agir d’un serveur NFS unique déployé dans le cloud ou d’un serveur NFS local accessible via un réseau virtuel. Les montages NFS prennent en charge [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md). Average vFXT est une solution de mise en cache en mémoire distribuée pour les tâches de calcul haute performance (HPC) gourmandes en ressources et d’autres interfaces standard compatibles avec NFS. Par exemple, [NFS pour Azure Blob](../storage/blobs/network-file-system-protocol-support.md) et [NFS pour Azure Files](../storage/files/storage-files-how-to-mount-nfs-shares.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=3.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Common Internet File System

Le montage de [CIFS (Common Internet File System)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) sur des nœuds de pool est un autre moyen de fournir l’accès aux systèmes de fichiers traditionnels. CIFS est un protocole de partage de fichiers qui fournit un mécanisme ouvert et multiplateforme pour la demande de fichiers et de services de serveur réseau. CIFS est basé sur la version améliorée du protocole [SMB](/windows-server/storage/file-server/file-server-smb-overview) pour le partage de fichiers intranet et Internet.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnostiquer les erreurs de montage

Si une configuration de montage échoue, le nœud de calcul dans le pool échoue et l’état du nœud est défini sur `unusable`. Pour diagnostiquer un échec de configuration de montage, examinez la propriété [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) pour plus d’informations sur l’erreur.

Pour obtenir les fichiers journaux de débogage, utilisez [OutputFiles](batch-task-output-files.md) pour télécharger les fichiers `*.log`. Les fichiers `*.log` contiennent des informations sur le montage du système de fichiers à l’emplacement `AZ_BATCH_NODE_MOUNTS_DIR`. Les fichiers journaux de montage ont le format : `<type>-<mountDirOrDrive>.log` pour chaque montage. Par exemple, un montage `cifs` dans un répertoire de montage nommé `test` aura un fichier journal de montage nommé `cifs-test.log`.

## <a name="support-matrix"></a>Matrice de prise en charge

Azure Batch prend en charge les types de systèmes de fichiers virtuels suivants pour les agents de nœud produits pour leur éditeur et offre respectifs.

| Type de système d'exploitation | Partage Azure Files | Conteneur de blobs Azure | Montage NFS | Montage CIFS |
|---|---|---|---|---|
| Linux | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="networking-requirements"></a>Configuration requise du réseau

Lorsque vous utilisez des montages de fichiers virtuels avec des [pools Azure batch dans un réseau virtuel](batch-virtual-network.md), gardez à l’esprit les exigences suivantes et assurez-vous qu’aucun trafic requis n’est bloqué.

- **Partages de fichiers Azure** :
  - Requiert l’ouverture du port TCP 445 pour le trafic vers/à partir de l’étiquette de service « stockage ». Pour plus d’informations, consultez [Utiliser un partage de fichiers Azure avec Windows](../storage/files/storage-how-to-use-files-windows.md#prerequisites).
- **Conteneurs d’objets Blob Azure** :
  - Requiert l’ouverture du port TCP 443 pour le trafic vers/à partir de l’étiquette de service « stockage ».
  - Les machines virtuelles doivent avoir accès à https://packages.microsoft.com pour télécharger les packages blobfuse et gpg. Selon votre configuration, vous pouvez également avoir besoin d’accéder à d’autres URL pour télécharger des packages supplémentaires.
- **NFS (Network File System)**  :
  - Nécessite l’accès au port 2049 (par défaut, votre configuration peut avoir d’autres exigences).
  - Les machines virtuelles doivent avoir accès au gestionnaire de package approprié pour télécharger le package `nfs-common` (pour Debian ou Ubuntu) ou `nfs-utils` (pour CentOS). Cette URL peut varier en fonction de la version de votre système d’exploitation. Selon votre configuration, vous pouvez également avoir besoin d’accéder à d’autres URL pour télécharger d’autres packages.
  - Le montage d’un objet BLOB Azure ou Azure Files via NFS peut avoir davantage d’exigence de mise en réseau. Par exemple, vous pouvez avoir besoin de nœuds de calcul qui partagent le même sous-réseau d’un réseau virtuel que le compte de stockage.
- **CIFS (Common Internet File System)**  :
  - Nécessite l’accès au port TCP 445.
  - Les machines virtuelles doivent avoir accès au ou aux gestionnaires de package appropriés pour télécharger le package `cifs-utils`. Cette URL peut varier en fonction de la version de votre système d’exploitation.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur le montage d’un partage de fichiers Azure Files avec [Windows](../storage/files/storage-how-to-use-files-windows.md) ou [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Découvrez-en davantage sur l’utilisation et le montage de systèmes de fichiers virtuels [blobfuse](https://github.com/Azure/azure-storage-fuse).
- Consultez [Vue d’ensemble du système de fichiers réseau](/windows-server/storage/nfs/nfs-overview) pour en savoir plus sur NFS et ses applications.
- Pour plus d’informations sur CIFS, consultez [Vue d’ensemble du protocole SMB et du protocole CIFS de Microsoft](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview).