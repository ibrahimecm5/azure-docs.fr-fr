---
title: Configurer une stratégie de gestion de cycle de vie
titleSuffix: Azure Storage
description: Configurez une stratégie de gestion de cycle de vie pour déplacer automatiquement les données entre les niveaux chaud, froid et archive pendant le cycle de vie des données.
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0fc4fce280db7a2d4744fc8466ff9297699494a3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460353"
---
# <a name="configure-a-lifecycle-management-policy"></a>Configurer une stratégie de gestion de cycle de vie

La gestion de cycle de vie de Stockage Azure offre une stratégie basée sur des règles que vous pouvez utiliser pour faire passer les données blob aux niveaux d’accès appropriés ou faire expirer les données à la fin de leur cycle de vie. Une stratégie de cycle de vie agit sur un blob de base et, éventuellement, sur les versions ou les instantanés du blob. Pour plus d’informations sur les stratégies de gestion de cycle de vie, consultez [Optimiser les coûts en gérant automatiquement le cycle de vie des données](lifecycle-management-overview.md).

Une stratégie de gestion de cycle de vie se compose d’une ou plusieurs règles qui définissent un ensemble d’actions à entreprendre en fonction de la satisfaction d’une condition. Pour un blob de base, vous pouvez choisir de vérifier l’une des deux conditions suivantes :

- Le nombre de jours depuis la dernière modification du blob.
- Le nombre de jours depuis le dernier accès au blob. Pour utiliser cette condition dans une action, vous devez d’abord [activer le suivi de l’heure d’accès (facultatif)](#optionally-enable-access-time-tracking).

Lorsque la condition sélectionnée est vraie, la stratégie de gestion exécute l’action spécifiée. Par exemple, si vous avez défini une action pour déplacer un blob du niveau chaud au niveau froid s’il n’a pas été modifié depuis 30 jours, la stratégie de gestion de cycle de vie déplace le blob 30 jours après la dernière opération d’écriture sur ce blob.

Pour un instantané ou une version de blob, la condition vérifiée est le nombre de jours écoulés depuis la création de l’instantané ou de la version.

## <a name="optionally-enable-access-time-tracking"></a>Activer le suivi de l’heure d’accès (facultatif)

Avant de configurer une stratégie de gestion de cycle de vie, vous pouvez choisir d’activer le suivi de l’heure d’accès au blob. Lorsque le suivi de l’heure d’accès est activé, une stratégie de gestion de cycle de vie peut inclure une action basée sur l’heure du dernier accès au blob par une opération de lecture ou d’écriture.

#### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour activer le suivi de l’heure du dernier accès avec le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Dans la section **Gestion des données**, sélectionnez **Activer le suivi d’accès**.

    :::image type="content" source="media/lifecycle-management-policy-configure/last-access-tracking-enable.png" alt-text="Capture d’écran montrant comment activer le suivi du dernier accès dans le portail Azure":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour activer le suivi de l’heure du dernier accès avec PowerShell, appelez la commande [Enable-AzStorageBlobLastAccessTimeTracking](/powershell/module/az.storage/enable-azstoragebloblastaccesstimetracking), comme indiqué dans l’exemple suivant. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Enable-AzStorageBlobLastAccessTimeTracking  -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -PassThru
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour activer le suivi de l’heure du dernier accès avec Azure CLI, appelez la commande [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update), comme indiqué dans l’exemple suivant. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --enable-last-access-tracking true
```

# <a name="template"></a>[Modèle](#tab/template)

Pour activer le suivi de l’heure du dernier accès pour un compte de stockage nouveau ou existant avec un modèle Azure Resource Manager, incluez l’objet **lastAccessTimeTrackingPolicy** dans la définition du modèle. Pour plus d’informations, consultez la [référence des modèles Bicep et ARM Microsoft.Storage/storageAccounts/blobServices 2021-02-01](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/blobservices?tabs=json). L’objet **lastAccessTimeTrackingPolicy** est disponible dans l’API REST du fournisseur de ressources Stockage Azure pour les versions 2019-06-01 et ultérieures.

---

Utilisez la propriété **daysAfterLastAccessTimeGreaterThan** pour spécifier le nombre de jours depuis le dernier accès après lequel une action doit être prise sur un blob.

## <a name="create-or-manage-a-policy"></a>Créer ou gérer une stratégie

Vous pouvez ajouter, modifier ou supprimer une stratégie de gestion de cycle de vie avec le portail Azure, PowerShell, Azure CLI ou un modèle Azure Resource Manager.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Il existe deux façons d’ajouter une stratégie à l’aide du Portail Microsoft Azure.

- [Vue Liste](#list-view)
- [Mode code](#code-view)

#### <a name="list-view"></a>Affichage Liste

1. Dans le portail Azure, accédez à votre compte de stockage.
1. Sous **Gestion des données**, sélectionnez **Gestion de cycle de vie** pour afficher ou modifier les stratégies de gestion de cycle de vie.
1. Sélectionnez l’onglet **Mode Liste**.

1. Sélectionnez **Ajouter une règle** et nommez votre règle dans le formulaire **Détails**. Vous pouvez également définir les l’**Étendue de la règle**, le **Type d’objet blob** et le **Sous-type d’objet blob**. L’exemple suivant définit l’étendue pour filtrer les objets blob. Cela entraîne l’ajout de l’onglet **Jeu de filtres**.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-details.png" alt-text="Gestion du cycle de vie - Ajouter une page Détails de la règle dans le Portail Azure":::

1. Sélectionnez **Objets blob de base** pour définir les conditions de votre règle. Dans l’exemple suivant, les objets BLOB sont déplacés vers le stockage froid s’ils n’ont pas été modifiés depuis 30 jours.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-base-blobs.png" alt-text="Page Gestion de cycle de vie des objets blob de base du Portail Microsoft Azure":::

   L’option **Dernier accès** est disponible uniquement si vous avez activé le suivi de l’heure d’accès. Pour savoir comment activer le suivi d’accès, consultez la section [Activer le suivi de l’heure d’accès (facultatif)](#optionally-enable-access-time-tracking).

1. Si vous avez sélectionné **Limiter les objets blob avec des filtres** dans la page **Détails**, sélectionnez **Jeu de filtres** pour ajouter un filtre facultatif. L’exemple suivant filtre les blobs dont le nom commence par *log* dans un conteneur appelé *sample-container*.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-filter-set.png" alt-text="Page Lifecycle management action set (Jeu de filtres de gestion du cycle de vie) du Portail Microsoft Azure":::

1. Sélectionnez **Ajouter** pour ajouter la stratégie.

#### <a name="code-view"></a>Mode code

1. Dans le portail Azure, accédez à votre compte de stockage.
1. Sous **Gestion des données**, sélectionnez **Gestion de cycle de vie** pour afficher ou modifier les stratégies de gestion de cycle de vie.
1. Sélectionnez l’onglet **Mode code**. Dans cet onglet, vous pouvez définir une stratégie de gestion de cycle de vie au format JSON.

L’exemple JSON suivant définit une stratégie de cycle de vie qui déplace un objet blob de blocs dont le nom commence par *log* vers le niveau froid s’il a été modifié pour la dernière fois il y a plus de 30 jours.

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "sample-container/log"
             ]
           }
         }
       }
     ]
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour ajouter une stratégie de gestion de cycle de vie avec PowerShell, utilisez ces commandes :

- Appelez la commande [Add-AzStorageAccountManagementPolicyAction](/powershell/module/az.storage/add-azstorageaccountmanagementpolicyaction) pour définir les actions qui composent une règle.
- Appelez la commande [New-AzStorageAccountManagementPolicyFilter](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyfilter) pour spécifier un ou plusieurs filtres pour une règle.
- Appelez la commande [New-AzStorageAccountManagementPolicyRule](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyrule) pour créer une règle de stratégie qui comprend des actions et des filtres facultatifs.
- Appelez la commande [Set-AzStorageAccountManagementPolicy](/powershell/module/az.storage/set-azstorageaccountmanagementpolicy) pour créer la stratégie sur le compte de stockage.

L’exemple suivant montre comment utiliser chacune de ces commandes pour créer une stratégie de cycle de vie. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
# Initialize the following variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Create a new action object.
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete `
    -daysAfterModificationGreaterThan 180
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToArchive `
    -daysAfterModificationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToCool `
    -daysAfterModificationGreaterThan 30
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -SnapshotAction Delete `
    -daysAfterCreationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BlobVersionAction TierToArchive `
    -daysAfterCreationGreaterThan 90

# Create a new filter object.
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd `
    -BlobType blockBlob

# Create a new rule object.
$rule1 = New-AzStorageAccountManagementPolicyRule -Name sample-rule `
    -Action $action `
    -Filter $filter

# Create the policy.
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Rule $rule1
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour ajouter une stratégie de gestion de cycle de vie avec Azure CLI, écrivez la stratégie dans un fichier JSON, puis appelez la commande [az storage account management-policy create](/cli/azure/storage/account/management-policy#az_storage_account_management_policy_create) pour créer la stratégie.

L’exemple suivant montre comment utiliser chacune de ces commandes pour créer une stratégie de cycle de vie. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage account management-policy create \
    --account-name <storage-account> \
    --policy @policy.json \
    --resource-group <resource-group>
```

# <a name="template"></a>[Modèle](#tab/template)

Pour définir une stratégie de gestion de cycle de vie avec un modèle Azure Resource Manager, incluez l’objet **Microsoft.Storage/storageAccounts/managementPolicies** dans votre modèle. Pour plus d’informations sur la configuration, consultez la [référence des modèles Bicep et ARM Microsoft.Storage/storageAccounts/managementPolicies 2021-02-01](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/managementpolicies?tabs=json). L’objet **Microsoft.Storage/storageAccounts/managementPolicies** est disponible dans l’API REST du fournisseur de ressources Stockage Azure pour les versions 2018-11-01 et ultérieures.

---

Une stratégie de gestion de cycle de vie doit être lue ou écrite dans son intégralité. Les mises à jour partielles ne sont pas prises en charge.

> [!NOTE]
> Si vous activez les règles de pare-feu de votre compte de stockage, les requêtes de gestion du cycle de vie peuvent être bloquées. Vous pouvez débloquer ces requêtes en fournissant des exceptions pour les services Microsoft approuvés. Pour plus d’informations, consultez la section **Exceptions** dans [Configurer des pare-feu et des réseaux virtuels](../common/storage-network-security.md#exceptions).

## <a name="see-also"></a>Voir aussi

- [Optimiser les coûts en gérant automatiquement le cycle de vie des données](lifecycle-management-overview.md)
- [Niveaux d’accès chaud, froid et archive pour les données d’objet blob](access-tiers-overview.md)
