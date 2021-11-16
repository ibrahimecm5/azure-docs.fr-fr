---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: fa07459629d832516fda5d3141f85b23afe3fbb8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861616"
---
Un fournisseur de ressources est un service qui fournit des ressources Azure. Utilisez la commande Azure CLI [az provider register](/cli/azure/provider#az_provider_register) ou l’applet de commande Azure PowerShell [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) pour inscrire le fournisseur de ressources du Registre confidentiel Azure, « microsoft.ConfidentialLedger ».

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider register --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Register-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---

Vous pouvez vérifier que l’inscription est terminée à l’aide de la commande Azure CLI [az provider register](/cli/azure/provider#az_provider_show) ou de la cmdlet Azure PowerShell [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider show --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---
