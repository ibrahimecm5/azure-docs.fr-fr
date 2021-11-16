---
title: Inscrire un principal de service de registre à l’aide de Registre confidentiel Microsoft Azure
description: Inscrire un principal de service de registre à l’aide de Registre confidentiel Microsoft Azure
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 843c2489052ed1df78c738b680740d2a7206e4a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454501"
---
# <a name="register-a-confidential-ledger-service-principal"></a>Inscrire un principal de service Registre confidentiel

Pour associer un compte de stockage à votre registre confidentiel, vous devez d’abord enregistrer le principal de service Registre confidentiel.

## <a name="create-a-service-principal"></a>Créer un principal du service

Pour créer un principal du service, exécutez la commande Azure CLI [az ad sp create](/cli/azure/ad/sp#az_ad_sp_create) ou les cmdlets Azure PowerShell [Connect-AzureAD](/powershell/module/azuread/connect-azuread) et [New-AzureADServicePrincipal](/powershell/module/azuread/new-azureadserviceprincipal).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az ad sp create --id 4353526e-1c33-4fcf-9e82-9683edf52848
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Connect-AzureAD -TenantId "<tenant-id-of-customer>"
New-AzureADServicePrincipal -AppId 4353526e-1c33-4fcf-9e82-9683edf52848 -DisplayName ConfidentialLedger
```
---

## <a name="assign-roles"></a>Attribuer des rôles

Définissez le rôle « [Contributeur aux données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) » de Gestion des identités et des accès pour le principal du service de Registre confidentiel pour le compte de stockage. Pour ce faire, vous pouvez utiliser la commande Azure CLI [az role assignment create](/cli/azure/role/assignment) ou la cmdlet Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az role assignment create --role "Storage Blob Data Contributor" --assignee "4353526e-1c33-4fcf-9e82-9683edf52848" --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
New-AzRoleAssignment -ApplicationId 4353526e-1c33-4fcf-9e82-9683edf52848 -RoleDefinitionName "Storage Blob Data Contributor" -Scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
---

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de Registre confidentiel Microsoft Azure](overview.md)
