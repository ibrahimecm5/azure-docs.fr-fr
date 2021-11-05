---
title: Inscrire à l’extension SQL IaaS Agent (Linux)
description: Découvrez comment inscrire votre machine virtuelle Azure SQL Server sur Linux à l’extension SQL IaaS Agent pour activer les fonctionnalités Azure, ainsi que pour la conformité et une meilleure facilité de gestion.
services: virtual-machines-windows
documentationcenter: na
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-Linux-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 2cf9219f5afc6805c147243ec0157280c839dc87
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098040"
---
# <a name="register-linux-sql-server-vm-with-sql-iaas-agent-extension"></a>Inscrire une machine virtuelle SQL Server Linux à l’extension SQL IaaS Agent 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-agent-extension-manually-register-single-vm.md)
> * [Linux](sql-iaas-agent-extension-register-vm-linux.md)


Inscrivez votre machine virtuelle SQL Server à l’[extension SQL IaaS Agent](sql-server-iaas-agent-extension-linux.md) pour bénéficier d’une multitude de fonctionnalités pour votre machine virtuelle Azure SQL Server sur Linux.

## <a name="overview"></a>Vue d’ensemble

S’inscrire auprès de [l’extension SQL Server IaaS Agent](sql-server-iaas-agent-extension-linux.md) crée la _ressource_ de **machine virtuelle SQL** dans votre abonnement ; il s’agit d’une ressource _distincte_ de la ressource de machine virtuelle. Le fait de désinscrire votre machine virtuelle SQL Server de l’extension supprime la _ressource_ de **machine virtuelle SQL**, mais pas la machine virtuelle elle-même.

Pour utiliser l’extension SQL IaaS Agent, vous devez d’abord [inscrire votre abonnement auprès du fournisseur **Microsoft.SqlVirtualMachine**](#register-subscription-with-rp), ce qui donne à l’extension SQL IaaS Agent la capacité de créer des ressources dans cet abonnement spécifique.

> [!IMPORTANT]
> L’extension SQL IaaS Agent collecte des données dans le seul but de fournir d’autres avantages aux clients lors de l’utilisation de SQL Server dans Machines virtuelles Azure. Microsoft n’utilisera pas ces données pour les audits de gestion des licences sans le consentement préalable du client. Pour plus d’informations, consultez l’[Avenant à la déclaration de confidentialité de SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

## <a name="prerequisites"></a>Prérequis

Pour inscrire votre machine virtuelle SQL Server auprès de l’extension, voici ce dont vous avez besoin :

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Une [machine virtuelle Ubuntu Linux](../../../virtual-machines/linux/quick-create-portal.md) de modèle de ressource Azure avec [SQL Server 2017 (ou ultérieur)](https://www.microsoft.com/sql-server/sql-server-downloads), déployée sur le cloud public ou le cloud Azure Government.
- La version la plus récente d’[Azure CLI](/cli/azure/install-azure-cli) ou d’[Azure PowerShell (5.0 minimum)](/powershell/azure/install-az-ps).

## <a name="register-subscription-with-rp"></a>Inscrire un abonnement auprès d’un fournisseur de ressources

Pour inscrire votre machine virtuelle SQL Server auprès de l’extension SQL IaaS Agent, vous devez d’abord inscrire votre abonnement auprès du fournisseur de ressources (RP) **Microsoft.SqlVirtualMachine**. Ainsi, l’extension SQL IaaS Agent peut créer des ressources dans votre abonnement. Pour ce faire, vous pouvez utiliser le portail Azure, l’interface Azure CLI ou Azure PowerShell.

### <a name="azure-portal"></a>Portail Azure

Inscrivez votre abonnement auprès du fournisseur de ressources en utilisant le portail Azure :

1. Ouvrez le portail Azure et accédez à **Tous les services**.
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.
1. Sur la page **Abonnements**, sélectionnez **Fournisseurs de ressources** sous **Paramètres**.
1. Entrez **sql** dans le filtre pour afficher les fournisseurs de ressources liées à SQL.
1. Sélectionnez **Inscrire**, **Réinscrire** ou **Désinscrire** pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée.


![Modifier le fournisseur](../windows/media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

### <a name="command-line"></a>Ligne de commande

Inscrivez votre abonnement Azure auprès du fournisseur **Microsoft.SqlVirtualMachine** en utilisant Azure CLI ou Azure PowerShell.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Inscrivez votre abonnement auprès du fournisseur de ressources en utilisant Azure CLI : 

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Inscrivez votre abonnement auprès du fournisseur de ressources en utilisant Azure PowerShell : 

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-vm"></a>Inscrire la machine virtuelle

L’extension SQL IaaS Agent sur Linux est disponible uniquement en mode léger, lequel prend seulement en charge le changement de type de licence et d’édition de SQL Server. Utilisez Azure CLI ou Azure PowerShell pour inscrire votre machine virtuelle SQL Server avec l’extension en mode léger pour des fonctionnalités limitées. 

Indiquez une licence SQL Server de type paiement à l’utilisation (`PAYG`) pour payer en fonction de l’utilisation, Azure Hybrid Benefit (`AHUB`) pour utiliser votre propre licence ou récupération d’urgence (`DR`) pour activer la [licence de réplica de récupération d’urgence gratuite](../windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Inscrire une machine virtuelle SQL Server en mode allégé avec l’interface de ligne de commande Azure :

```azurecli-interactive
# Register Enterprise or Standard self-installed VM in Lightweight mode
az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Inscrire une machine virtuelle SQL Server en mode léger avec Azure PowerShell :

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType <license_type>  -SqlManagementType LightWeight  
```

---

## <a name="verify-registration-status"></a>Vérifier l’état de l’inscription

Vous pouvez vérifier si votre machine virtuelle SQL Server a déjà été inscrite auprès de l’extension SQL IaaS Agent via le portail Azure, l’interface de ligne de commande Azure ou Azure PowerShell.


### <a name="azure-portal"></a>Portail Azure

Vérifiez l’état de l’inscription avec le portail Azure : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à votre ressource machines virtuelles SQL.
1. Sélectionnez votre machine virtuelle SQL Server dans la liste. Si votre machine virtuelle SQL Server n’est pas listée ici, il est probable qu’elle n’a pas été inscrite auprès de l’extension SQL IaaS Agent.

### <a name="command-line"></a>Ligne de commande

Vérifiez l’état d’inscription actuel d’une machine virtuelle SQL Server à l’aide d’Azure CLI ou d’Azure PowerShell. `ProvisioningState` affiche `Succeeded` si l’inscription a réussi.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Vérifiez l’état de l’inscription en utilisant Azure CLI :

```azurecli-interactive
az sql vm show -n <vm_name> -g <resource_group>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Vérifiez l’état de l’inscription en utilisant Azure PowerShell :

```powershell-interactive
Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
```

---

Une erreur indique que la machine virtuelle SQL Server n’a pas été inscrite auprès de l’extension.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Questions fréquentes (FAQ) pour SQL Server sur une machine virtuelle Windows](frequently-asked-questions-faq.yml)
* [Guide des tarifs pour SQL Server sur une machine virtuelle Windows](../windows/pricing-guidance.md)
* [Notes de publication pour SQL Server sur une machine virtuelle Windows](../windows/doc-changes-updates-release-notes.md)
