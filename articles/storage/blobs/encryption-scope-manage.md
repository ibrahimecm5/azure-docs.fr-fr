---
title: Créer et gérer des étendues de chiffrement
description: Découvrez comment créer une étendue de chiffrement pour isoler des données blob au niveau du conteneur ou du blob.
services: storage
author: tamram
ms.service: storage
ms.date: 05/10/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 283fd9dc199f86992be9f3952cf3a82b916ae56c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570908"
---
# <a name="create-and-manage-encryption-scopes"></a>Créer et gérer des étendues de chiffrement

Les étendues de chiffrement vous permettent de gérer le chiffrement au niveau d’un objet blob ou d’un conteneur individuel. Vous pouvez utiliser des étendues de chiffrement pour créer des limites sécurisées entre les données qui résident dans le même compte de stockage mais qui appartiennent à des clients différents. Pour plus d’informations sur les étendues de chiffrement, consultez [Étendues de chiffrement pour le stockage d’objets blob](encryption-scope-overview.md).

Cet article montre comment créer une étendue de chiffrement. Il montre également comment spécifier une étendue de chiffrement lorsque vous créez un objet blob ou un conteneur.

## <a name="create-an-encryption-scope"></a>Créer une étendue de chiffrement

Vous pouvez créer une étendue de chiffrement protégée par une clé gérée par Microsoft ou une clé gérée par le client qui est stockée dans un coffre de clés Azure Key Vault ou un module de sécurité matériel (HSM) Azure Key Vault Managed Hardware Security Model. Pour créer une étendue de chiffrement avec une clé gérée par le client, vous devez d’abord créer un coffre de clés ou un HSM managé et ajouter la clé que vous prévoyez d’utiliser pour l’étendue. Le coffre de clés ou le HSM managé doit avoir la protection contre le vidage activée et se trouver dans la même région que le compte de stockage.

Une étendue de chiffrement est automatiquement activée lorsque vous la créez. Après avoir créé l’étendue de chiffrement, vous pouvez la spécifier lorsque vous créez un objet blob. Vous pouvez également spécifier une étendue de chiffrement par défaut lorsque vous créez un conteneur, qui s’applique automatiquement à tous les objets blob du conteneur.

# <a name="portal"></a>[Portail](#tab/portal)

Pour créer une étendue de chiffrement dans le Portail Azure, effectuez les étapes suivantes :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sélectionnez le paramètre de **Chiffrement**.
1. Sélectionnez l'onglet **Étendues de chiffrement**.
1. Cliquez sur le bouton **Ajouter** pour ajouter une nouvelle étendue de chiffrement.
1. Dans le volet **Créer une étendue de chiffrement**, entrez un nom pour la nouvelle étendue.
1. Sélectionnez le type souhaité de support de clé chiffrement, soit des **clés gérées par Microsoft**, soit des **clés gérées par le client**.
    - Si vous avez sélectionné **Clés gérées par Microsoft**, cliquez sur **Créer** pour créer l’étendue de chiffrement.
    - Si vous avez sélectionné **Clés gérées par le client**, choisissez un abonnement et spécifiez un coffre de clés ou un HSM managé et une clé à utiliser pour cette étendue de chiffrement.
1. Si le chiffrement d'infrastructure est activé pour le compte de stockage, il est automatiquement activé pour la nouvelle étendue de chiffrement. Sinon, vous pouvez choisir d'activer ou non le chiffrement d'infrastructure pour l'étendue de chiffrement.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Capture d’écran montrant comment créer une file d’attente dans le Portail Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour créer une étendue de chiffrement avec PowerShell, installez d’abord le module PowerShell [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage), version 3.4.0 ou ultérieure.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Créer une étendue de chiffrement protégée par des clés gérées par Microsoft

Pour créer une étendue de chiffrement protégée par des clés gérées par Microsoft, appelez la commande [New-AzStorageEncryptionScope](/powershell/module/az.storage/new-azstorageencryptionscope) avec le paramètre `-StorageEncryption`.

Si le chiffrement d'infrastructure est activé pour le compte de stockage, il est automatiquement activé pour la nouvelle étendue de chiffrement. Sinon, vous pouvez choisir d'activer ou non le chiffrement d'infrastructure pour l'étendue de chiffrement. Pour créer la nouvelle étendue en activant le chiffrement d'infrastructure, incluez le paramètre `-RequireInfrastructureEncryption`.

N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs :

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Créer une étendue de chiffrement protégée par des clés gérées par clients

Pour créer une nouvelle étendue de chiffrement protégée par des clés gérées par le client stockées dans un coffre de clés ou un HSM managé, configurez d’abord les clés gérées par le client pour le compte de stockage. Vous devez affecter une identité gérée au compte de stockage, puis utiliser l’identité gérée pour configurer la stratégie d’accès du coffre de clés ou un HSM managé afin que le compte de stockage dispose des autorisations nécessaires pour y accéder.

Pour configurer des clés gérées par le client en vue d’une utilisation avec une étendue de chiffrement, la protection contre le vidage doit être activée sur le coffre de clés ou le HSM managé. Le coffre de clés ou le HSM managé doit se trouver dans la même région que le compte de stockage.

N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs :

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Ensuite, appelez la commande [New-AzStorageEncryptionScope](/powershell/module/az.storage/new-azstorageencryptionscope) avec le paramètre `-KeyvaultEncryption` et spécifiez l’URI de la clé. L’inclusion de la version de la clé sur l’URI de la clé est facultative. Si vous omettez la version de la clé, l’étendue de chiffrement utilisera automatiquement la version la plus récente de la clé. Si vous incluez la version de la clé, vous devez mettre à jour la version de clé manuellement pour utiliser une version différente.

Si le chiffrement d'infrastructure est activé pour le compte de stockage, il est automatiquement activé pour la nouvelle étendue de chiffrement. Sinon, vous pouvez choisir d'activer ou non le chiffrement d'infrastructure pour l'étendue de chiffrement. Pour créer la nouvelle étendue en activant le chiffrement d'infrastructure, incluez le paramètre `-RequireInfrastructureEncryption`.

N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs :

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pour créer une étendue de chiffrement avec Azure CLI, commencez par installer la version 2.20.0 ou ultérieure d’Azure CLI.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Créer une étendue de chiffrement protégée par des clés gérées par Microsoft

Pour créer une étendue de chiffrement protégée par des clés gérées par Microsoft, appelez la commande [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) avec le paramètre `--key-source` comme `Microsoft.Storage`.

Si le chiffrement d'infrastructure est activé pour le compte de stockage, il est automatiquement activé pour la nouvelle étendue de chiffrement. Sinon, vous pouvez choisir d'activer ou non le chiffrement d'infrastructure pour l'étendue de chiffrement. Pour créer la nouvelle étendue en activant le chiffrement d'infrastructure, incluez le paramètre `--require-infrastructure-encryption` et définissez sa valeur sur `true`.

N’oubliez pas de remplacer les valeurs d’espace réservé par vos propres valeurs :

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Créer une étendue de chiffrement protégée par des clés gérées par clients

Pour créer une nouvelle étendue de chiffrement protégée par des clés gérées par le client dans un coffre de clés ou un HSM managé, configurez d’abord les clés gérées par le client pour le compte de stockage. Vous devez affecter une identité gérée au compte de stockage, puis utiliser l’identité gérée pour configurer la stratégie d’accès du coffre de clés afin que le compte de stockage dispose des autorisations nécessaires pour y accéder. Pour plus d’informations, consultez [Clés gérées par le client pour le chiffrement Stockage Azure](../common/customer-managed-keys-overview.md).

Pour configurer des clés gérées par le client en vue d’une utilisation avec une étendue de chiffrement, la protection contre le vidage doit être activée sur le coffre de clés ou le HSM managé. Le coffre de clés ou le HSM managé doit se trouver dans la même région que le compte de stockage.

N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs :

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Ensuite, appelez la commande [az storage account encryption-scope](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) avec le paramètre `--key-uri` et spécifiez l'URI de la clé. L’inclusion de la version de la clé sur l’URI de la clé est facultative. Si vous omettez la version de la clé, l’étendue de chiffrement utilisera automatiquement la version la plus récente de la clé. Si vous incluez la version de la clé, vous devez mettre à jour la version de clé manuellement pour utiliser une version différente.

Si le chiffrement d'infrastructure est activé pour le compte de stockage, il est automatiquement activé pour la nouvelle étendue de chiffrement. Sinon, vous pouvez choisir d'activer ou non le chiffrement d'infrastructure pour l'étendue de chiffrement. Pour créer la nouvelle étendue en activant le chiffrement d'infrastructure, incluez le paramètre `--require-infrastructure-encryption` et définissez sa valeur sur `true`.

N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs :

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Pour apprendre à configurer le chiffrement Azure Storage avec des clés gérées par le client dans un coffre de clés ou un HSM managé, consultez les articles suivants :

- [Configurer le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md)
- [Configurer le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault Managed HSM](../common/customer-managed-keys-configure-key-vault-hsm.md).

Pour en savoir plus sur le chiffrement d'infrastructure, consultez [Activer le chiffrement d'infrastructure pour le chiffrement double des données](../common/infrastructure-encryption-enable.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Répertorier les étendues de chiffrement pour un compte de stockage

# <a name="portal"></a>[Portail](#tab/portal)

Pour afficher les étendues de chiffrement pour un compte de stockage dans le Portail Azure, accédez au paramètre **Étendues de chiffrement** pour le compte de stockage. Dans ce volet, vous pouvez activer ou désactiver une étendue de chiffrement ou modifier la clé d’une étendue de chiffrement.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Capture d’écran montrant une liste des étendues de chiffrement dans le Portail Azure":::

Pour afficher les détails d’une clé gérée par le client, y compris l’URI et la version de la clé, et si la version de la clé est mise à jour automatiquement, suivez le lien dans la colonne **Clé**.

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="Capture d’écran montrant les détails d’une clé utilisée avec une étendue de chiffrement":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour obtenir la liste des étendues de chiffrement disponibles pour un compte de stockage avec PowerShell, appelez la commande **Get-AzStorageEncryptionScope**. N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs :

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Pour répertorier toutes les étendues de chiffrement dans un groupe de ressources par compte de stockage, utilisez la syntaxe de pipeline :

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pour répertorier les étendues de chiffrement disponibles pour un compte de stockage avec Azure CLI, appelez la commande [az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_list). N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs :

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Créer un conteneur avec une étendue de chiffrement par défaut

Lorsque vous créez un conteneur, vous pouvez spécifier une étendue de chiffrement par défaut. Les objets blob de ce conteneur utiliseront cette étendue par défaut.

Un objet blob individuel peut être créé avec sa propre étendue de chiffrement, sauf si le conteneur est configuré pour exiger que tous les objets blob utilisent la portée par défaut. Pour plus d’informations, consultez [Étendues de chiffrement pour les conteneurs et les objets blob](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs).

# <a name="portal"></a>[Portail](#tab/portal)

Pour créer un conteneur avec une étendue de chiffrement par défaut dans le Portail Azure, commencez par créer l’étendue de chiffrement comme décrit dans [Créer une étendue de chiffrement](#create-an-encryption-scope). Ensuite, effectuez les étapes suivantes pour créer le conteneur :

1. Accédez à la liste des conteneurs dans votre compte de stockage, puis sélectionnez le bouton **Ajouter** pour créer un conteneur.
1. Développez les paramètres **Avancés** dans le volet **Nouveau conteneur**.
1. Dans la liste déroulante **Étendue de chiffrement**, sélectionnez l’étendue de chiffrement par défaut pour le conteneur.
1. Pour exiger que tous les objets blob du conteneur utilisent l’étendue de chiffrement par défaut, activez la case à cocher pour **Utiliser cette étendue de chiffrement pour tous les objets blob dans le conteneur.** Si cette case à cocher est activée, un objet blob individuel dans le conteneur ne peut pas remplacer l’étendue de chiffrement par défaut.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Capture d’écran montrant le conteneur avec l’étendue de chiffrement par défaut":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour créer un conteneur avec une étendue de chiffrement par défaut avec PowerShell, appelez la commande [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer), en spécifiant l’étendue du paramètre `-DefaultEncryptionScope`. Pour forcer tous les objets blob d’un conteneur à utiliser l’étendue par défaut du conteneur, définissez le paramètre `-PreventEncryptionScopeOverride` sur `true`.

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pour créer un conteneur avec une étendue de chiffrement par défaut avec Azure CLI, appelez la commande [az storage container create](/cli/azure/storage/container#az_storage_container_create), en spécifiant l’étendue du paramètre `--default-encryption-scope`. Pour forcer tous les objets blob d’un conteneur à utiliser l’étendue par défaut du conteneur, définissez le paramètre `--prevent-encryption-scope-override` sur `true`.

L’exemple suivant utilise votre compte Azure AD pour autoriser l’opération à créer le conteneur. Vous pouvez également utiliser la clé d'accès au compte. Pour plus d’informations, consultez [Autoriser l’accès aux données d’objet blob et de file d’attente avec Azure CLI](./authorize-data-operations-cli.md).

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Si un client tente de spécifier une étendue lors du téléchargement d’un objet blob vers un conteneur qui a une étendue de chiffrement par défaut et si le conteneur est configuré pour empêcher les objets blob de remplacer l’étendue par défaut, l’opération échoue avec un message indiquant que la requête est interdite par la stratégie de chiffrement de conteneur.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Charger un objet blob avec une étendue de chiffrement

Lorsque vous téléchargez un objet blob, vous pouvez spécifier une étendue de chiffrement pour cet objet ou utiliser l’étendue de chiffrement par défaut pour le conteneur, le cas échéant.

# <a name="portal"></a>[Portail](#tab/portal)

Pour créer un objet blob avec une étendue de chiffrement dans le Portail Azure, commencez par créer l’étendue de chiffrement comme décrit dans [Créer une étendue de chiffrement](#create-an-encryption-scope). Ensuite, effectuez les étapes suivantes pour créer l’objet blob :

1. Accédez au conteneur dans lequel vous souhaitez télécharger l’objet blob.
1. Sélectionnez le bouton **Charger** et localisez l’objet blob à charger.
1. Développez les paramètres **Avancés** dans le volet **Charger blob**.
1. Recherchez la section de liste déroulante **Étendue de chiffrement**. Par défaut, l’objet blob est créé avec l’étendue de chiffrement par défaut pour le conteneur, si elle a été spécifiée. Si le conteneur exige que les blobs utilisent le champ de chiffrement par défaut, cette section est désactivée.
1. Pour spécifier une autre étendue pour l’objet blob que vous chargez, sélectionnez **Choisir une étendue existante**, puis sélectionnez l’étendue souhaitée dans la liste déroulante.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Capture d’écran montrant comment charger un objet blob avec une étendue de chiffrement":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour charger un objet blob avec une étendue de chiffrement dans PowerShell, appelez la commande [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) et indiquez l’étendue de chiffrement pour l’objet blob.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pour charger un objet blob avec une étendue de chiffrement spécifiée dans Azure CLI, appelez la commande [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) et fournissez l'étendue de chiffrement pour l’objet blob.

Si vous utilisez Azure Cloud Shell, suivez les étapes décrites dans [Télécharger un objet BLOB](storage-quickstart-blobs-cli.md#upload-a-blob) pour créer un fichier dans le répertoire racine. Vous pouvez ensuite télécharger ce fichier vers un objet blob à l’aide de l’exemple suivant.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Modifier la clé de chiffrement pour une étendue

Pour modifier la clé qui protège une étendue de chiffrement d’une clé gérée par Microsoft à une clé gérée par le client, commencez par vérifier que vous avez activé les clés gérées par le client avec Azure Key Vault ou un HSM Key Vault pour le compte de stockage. Pour plus d’informations, consultez [Configurer le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md) ou [Configurer le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Portail](#tab/portal)

Pour modifier la clé qui protège une étendue dans le Portail Azure, procédez comme suit :

1. Accédez à l’onglet **Étendues de chiffrement** pour afficher la liste des étendues de chiffrement pour le compte de stockage.
1. Sélectionnez le bouton **Plus** en regard de l’étendue que vous souhaitez modifier.
1. Dans le volet **Modifier l’étendue de chiffrement**, vous pouvez modifier le type de chiffrement de clé gérée par Microsoft en clé gérée par le client, ou vice versa.
1. Pour sélectionner une nouvelle clé gérée par le client, sélectionnez **Utiliser une nouvelle clé** et spécifiez le coffre de clés, la clé et la version de clé.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour modifier la clé qui protège une étendue de chiffrement d’une clé gérée par le client à une clé gérée par Microsoft avec PowerShell, appelez la commande **Update-AzStorageEncryptionScope** et transmettez le paramètre `-StorageEncryption` :

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Ensuite, appelez la commande **Update-AzStorageEncryptionScope** et transmettez les paramètres `-KeyUri` et `-KeyvaultEncryption` :

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pour modifier la clé qui protège une étendue de chiffrement d’une clé gérée par le client à une clé gérée par Microsoft avec Azure CLI, appelez la commande [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) et transmettez le paramètre `--key-source` avec la valeur `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Ensuite, appelez la commande **az storage account encryption-scope update**, transmettez le paramètre `--key-uri` et transmettez le paramètre `--key-source` avec la valeur `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Désactiver une étendue de chiffrement

Quand une étendue de chiffrement est désactivée, vous n’êtes plus facturé pour celle-ci. Désactivez les étendues de chiffrement qui ne sont pas nécessaires pour éviter les frais inutiles. Pour plus d'informations, consultez [Fonctionnalité de chiffrement du service Stockage Azure pour les données au repos](../common/storage-service-encryption.md).

# <a name="portal"></a>[Portail](#tab/portal)

Pour désactiver une étendue de chiffrement dans le Portail Azure, accédez au paramètre **Étendues de chiffrement** pour le compte de stockage, sélectionnez l’étendue de chiffrement souhaitée, puis sélectionnez **Désactiver**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour désactiver une étendue de chiffrement avec PowerShell, appelez la commande Update-AzStorageEncryptionScope et incluez le paramètre `-State` avec la valeur `disabled`, comme indiqué dans l’exemple suivant. Pour réactiver une étendue de chiffrement, appelez la même commande avec le paramètre `-State` défini sur `enabled`. N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs :

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pour désactiver une étendue de chiffrement avec Azure CLI, appelez la commande [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) et incluez le paramètre `--state` avec la valeur `Disabled`, comme indiqué dans l’exemple suivant. Pour réactiver une étendue de chiffrement, appelez la même commande avec le paramètre `--state` défini sur `Enabled`. N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Il n’est pas possible de supprimer une étendue de chiffrement. Pour éviter les coûts inattendus, veillez à désactiver les étendues de chiffrement dont vous n’avez pas besoin.

---

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](../common/storage-service-encryption.md)
- [Étendues de chiffrement pour le stockage d’objets blob](encryption-scope-overview.md)
- [Clés gérées par le client pour le chiffrement du Stockage Azure](../common/customer-managed-keys-overview.md)
- [Activer le chiffrement d'infrastructure pour le chiffrement double des données](../common/infrastructure-encryption-enable.md)
