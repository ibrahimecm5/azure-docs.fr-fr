---
title: Créer un partage NFS (préversion) - Azure Files
description: Découvrez comment créer un partage de fichiers Azure qui peut être monté à l’aide du protocole NFS.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f146d51cdd43b8c4a52285476e47d0c6237efe0f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131046600"
---
# <a name="how-to-create-an-nfs-share-preview"></a>Guide pratique pour créer un partage NFS (préversion)
Les partages de fichiers Azure sont des partages de fichiers entièrement gérés qui résident dans le cloud. Cet article explique comment créer un partage de fichiers qui utilise le protocole NFS (préversion).

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Non](../media/icons/no-icon.png) | ![Oui](../media/icons/yes-icon.png) |

## <a name="limitations"></a>Limites
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]


### <a name="regional-availability"></a>Disponibilité régionale
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Prérequis
- Les partages NFS acceptent uniquement les UID/GID numériques. Pour éviter que vos clients envoient des UID/GID alphanumériques, désactivez le mappage des ID.
- Les partages NFS ne sont accessibles qu’à partir de réseaux approuvés. Les connexions à votre partage NFS doivent provenir de l’une des sources suivantes :
    - [Créez un point de terminaison privé](storage-files-networking-endpoints.md#create-a-private-endpoint) (recommandé) ou [restreignez l’accès à votre point de terminaison public](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configurer un VPN point à site (P2S) sous Linux pour une utilisation avec Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Configurer un VPN site à site pour une utilisation avec Azure Files](storage-files-configure-s2s-vpn.md).
    - Configurez [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Si vous envisagez d’utiliser Azure CLI, [installez-en la dernière version](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Inscrire le protocole NFS 4.1

Vous devez d’abord vous inscrire à la fonctionnalité pour pouvoir créer des partages de fichiers Azure NFS. Vous ne pouvez pas créer de partages NFS dans des comptes de stockage qui ont été créés avant l’inscription.

Si vous utilisez le module Azure PowerShell ou Azure CLI, inscrivez votre fonctionnalité à l’aide des commandes suivantes :

# <a name="portal"></a>[Portail](#tab/azure-portal)
Utilisez Azure PowerShell ou Azure CLI pour inscrire la fonctionnalité NFS 4.1 pour Azure Files.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount
# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context
# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login
# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"
az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
az provider register \
    --namespace Microsoft.Storage
```

---

L’approbation d’inscription peut prendre jusqu’à une heure. Pour vérifier que l’inscription est terminée, utilisez les commandes suivantes :

# <a name="portal"></a>[Portail](#tab/azure-portal)
Utilisez Azure PowerShell ou Azure CLI pour vérifier l’inscription de la fonctionnalité NFS 4.1 pour Azure Files. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```
---

## <a name="create-a-filestorage-storage-account"></a>Créer un compte de stockage FileStorage
Actuellement, les partages NFS 4.1 sont uniquement disponibles en tant que partages de fichiers Premium. Pour déployer un partage de fichiers Premium avec prise en charge du protocole NFS 4.1, vous devez d’abord créer un compte de stockage FileStorage. Un compte de stockage est un objet de niveau supérieur dans Azure, qui représente un pool partagé de stockage pouvant être utilisé pour déployer plusieurs partages de fichiers Azure.

# <a name="portal"></a>[Portail](#tab/azure-portal)
Pour créer un compte de stockage FileStorage, accédez au portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Comptes de stockage** dans le menu de gauche.

    ![Sur la page principale du portail Azure, sélectionnez le compte de stockage.](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Sur la fenêtre **Comptes de stockage**, sélectionnez **Ajouter**.
1. Sélectionnez l’abonnement dans lequel créer le compte de stockage.
1. Sélectionnez le groupe de ressources dans lequel créer le compte de stockage
1. Ensuite, entrez un nom pour votre compte de stockage. Le nom choisi doit être unique dans tout Azure. Le nom doit aussi contenir entre 3 et 24 caractères, et uniquement des lettres minuscules et des chiffres.
1. Sélectionnez l’emplacement de votre compte de stockage ou utilisez l’emplacement par défaut.
1. Pour **Performances**, sélectionnez **Premium**.

    Vous devez sélectionner **Premium** pour que **Partages de fichiers** soit une option disponible dans la liste déroulante **Type de compte**.

1. Pour le **type de compte Premium**, choisissez **Partages de fichiers**.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Capture d’écran des performances Premium sélectionnées.":::

1. Laissez **Réplication** défini sur sa valeur par défaut, **Stockage localement redondant (LRS)** .
1. Cliquez sur **Vérifier + créer** pour passer en revue vos paramètres de compte de stockage et créer le compte.
1. Sélectionnez **Create** (Créer).

Une fois que votre ressource de compte de stockage a été créée, accédez-y.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour créer un compte de stockage FileStorage, ouvrez une invite PowerShell et exécutez les commandes suivantes, en vous remplaçant `<resource-group>` et `<storage-account>` par les valeurs appropriées pour votre environnement.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour créer un compte de stockage FileStorage, ouvrez votre terminal et exécutez les commandes suivantes, en vous remplaçant `<resource-group>` et `<storage-account>` par les valeurs appropriées pour votre environnement.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="disable-secure-transfer"></a>Désactiver le transfert sécurisé

Vous ne pouvez pas monter un partage de fichiers NFS, sauf si vous désactivez le transfert sécurisé.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez au compte de stockage que vous avez créé.
1. Sélectionnez **Configuration**.
1. Sous **Transfert sécurisé requis**, sélectionnez **Désactivé**.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png" alt-text="Capture de l'écran de configuration du compte de stockage, sur lequel le transfert sécurisé est désactivé. " lightbox="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $False
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only false
```
---

## <a name="create-an-nfs-share"></a>Créer un partage NFS

# <a name="portal"></a>[Portail](#tab/azure-portal)

Maintenant que vous avez créé un compte FileStorage et configuré la mise en réseau, vous pouvez créer un partage de fichiers NFS. Le processus est similaire à la création d’un partage SMB ; vous sélectionnez simplement **NFS** au lieu de **SMB** lors de la création du partage.

1. Accédez à votre compte de stockage et sélectionnez **Partages de fichiers**.
1. Sélectionnez **+ Partage de fichiers** pour créer un partage de fichiers.
1. Nommez votre partage de fichiers, puis sélectionnez une capacité approvisionnée.
1. Dans **Protocole**, sélectionnez **NFS**.
1. Pour **Squash racine** effectuez une sélection.

    - Squash racine (par défaut) : l’accès pour le superutilisateur distant (racine) est mappé à l’UID (65534) et au GID (65534).
    - Pas de squash racine : le superutilisateur distant (racine) reçoit l’accès en tant que racine.
    - Tous les types squash : tous les accès utilisateur sont mappés à l’UID (65534) et au GID (65534).
    
1. Sélectionnez **Create** (Créer).

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/files-nfs-create-share.png" alt-text="Capture d’écran du panneau de création de partage de fichiers.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Pour créer un partage de fichiers Premium avec le module Azure PowerShell, utilisez l’applet de commande [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare).

    > [!NOTE]
    > Les partages de fichiers Premium sont facturés à l’aide d’un modèle approvisionné. La taille approvisionnée du partage est spécifiée par le paramètre `QuotaGiB` ci-dessous. Pour plus d’informations, consultez [Comprendre le modèle approvisionné](understanding-billing.md#provisioned-model) et la [page de tarification d’Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour créer un partage de fichiers Premium avec l’interface de ligne de commande Azure, utilisez la commande [az storage share create](/cli/azure/storage/share-rm).

> [!NOTE]
> Les partages de fichiers Premium sont facturés à l’aide d’un modèle approvisionné. La taille approvisionnée du partage est spécifiée par le paramètre `quota` ci-dessous. Pour plus d’informations, consultez [Comprendre le modèle approvisionné](understanding-billing.md#provisioned-model) et la [page de tarification d’Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé un partage NFS, pour l’utiliser, vous devez le monter sur votre client Linux. Pour plus d’informations, consultez [Comment monter un partage NFS](storage-files-how-to-mount-nfs-shares.md).

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes liés aux partages de fichiers Azure NFS](storage-troubleshooting-files-nfs.md).
