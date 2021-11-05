---
title: S’inscrire à l’extension IaaS SQL (Windows)
description: Découvrez comment inscrire votre SQL Server sur la machine virtuelle Azure Windows avec l’extension Agent IaaS SQL pour activer les fonctionnalités Azure, ainsi que pour la conformité et la gestion améliorée.
services: virtual-machines-windows
documentationcenter: na
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 20177246aa0b39dc7a7c254c4dfb964d0444b631
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044016"
---
# <a name="register-windows-sql-server-vm-with-sql-iaas-extension"></a>Inscrire la machine virtuelle SQL Server Windows avec l’extension IaaS SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-agent-extension-manually-register-single-vm.md)
> * [Linux](../linux/sql-iaas-agent-extension-register-vm-linux.md)

Inscrivez votre machine virtuelle SQL Server avec l’[extension Agent IaaS SQL](sql-server-iaas-agent-extension-automate-management.md) pour déverrouiller une multitude de fonctionnalités pour votre SQL Server sur une machine virtuelle Azure Windows. 

Cet article vous apprend à inscrire une machine virtuelle SQL Server individuelle auprès de l’extension SQL IaaS Agent. Vous pouvez également inscrire toutes les machines virtuelles SQL Server dans une inscription [automatiquement](sql-agent-extension-automatic-registration-all-vms.md) ou [plusieurs machines virtuelles en bloc à l’aide d’un script](sql-agent-extension-manually-register-vms-bulk.md).

> [!NOTE]
> À compter de septembre 2021, l’inscription auprès de l’extension IaaS SQL en mode complet ne nécessite plus le redémarrage du service SQL Server. 

## <a name="overview"></a>Vue d’ensemble

S’inscrire auprès de [l’extension SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) crée la [**ressource** de _machine virtuelle SQL_](manage-sql-vm-portal.md) dans votre abonnement ; il s’agit d’une ressource _distincte_ de la ressource de machine virtuelle. Le fait de désinscrire votre machine virtuelle SQL Server de l’extension va supprimer la _ressource_ de **machine virtuelle SQL**, mais pas la machine virtuelle elle-même.

Pendant le déploiement d’une image de machine virtuelle SQL Server de la Place de marché Azure via le portail Azure, la machine virtuelle SQL Server est inscrite automatiquement à l’extension. Toutefois, si vous choisissez d’installer automatiquement SQL Server sur une machine virtuelle Azure ou de configurer une machine virtuelle Azure à partir d’un disque dur virtuel personnalisé, vous devez inscrire votre machine virtuelle SQL Server auprès de l’extension SQL IaaS Agent pour déverrouiller toutes les fonctionnalités et capacités de gestion.

Pour utiliser l’extension SQL IaaS Agent, vous devez d’abord [inscrire votre abonnement auprès du fournisseur **Microsoft.SqlVirtualMachine**](#register-subscription-with-rp), ce qui donne à l’extension SQL IaaS Agent la capacité de créer des ressources dans cet abonnement spécifique. Vous pouvez alors inscrire votre machine virtuelle SQL Server avec l’extension. 

Par défaut, les machines virtuelles Azure sur lesquelles SQL Server 2016 ou une version ultérieure sont installées sont automatiquement inscrites avec l’extension Agent IaaS SQL quand elles sont détectées par le [service CEIP (Programme d’amélioration du produit)](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server).  Pour plus d’informations, consultez l’[Avenant à la déclaration de confidentialité de SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

> [!IMPORTANT]
> L’extension SQL IaaS Agent collecte des données dans le seul but de fournir d’autres avantages aux clients lors de l’utilisation de SQL Server dans Machines virtuelles Azure. Microsoft n’utilisera pas ces données pour les audits de gestion des licences sans le consentement préalable du client. Pour plus d’informations, consultez l’[Avenant à la déclaration de confidentialité de SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

## <a name="prerequisites"></a>Prérequis

Pour inscrire votre machine virtuelle SQL Server auprès de l’extension, voici ce dont vous avez besoin :

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Une [machine virtuelle Windows Server 2008 (ou version ultérieure)](../../../virtual-machines/windows/quick-create-portal.md) de modèle de ressource Azure avec [SQL Server 2008 (ou version ultérieure)](https://www.microsoft.com/sql-server/sql-server-downloads), déployée sur le cloud public ou le cloud Azure Government.
- La version la plus récente d’[Azure CLI](/cli/azure/install-azure-cli) ou d’[Azure PowerShell (5.0 minimum)](/powershell/azure/install-az-ps).
- La version minimale 4.5.1 ou une version ultérieure de .NET Framework.

## <a name="register-subscription-with-rp"></a>Inscrire un abonnement auprès d’un fournisseur de ressources

Pour inscrire votre machine virtuelle SQL Server auprès de l’extension SQL IaaS Agent, vous devez d’abord inscrire votre abonnement auprès du fournisseur de ressources (RP) **Microsoft.SqlVirtualMachine**. Ainsi, l’extension SQL IaaS Agent peut créer des ressources dans votre abonnement. Pour ce faire, vous pouvez utiliser le portail Azure, l’interface Azure CLI ou Azure PowerShell.

### <a name="azure-portal"></a>Portail Azure

Inscrivez votre abonnement auprès du fournisseur de ressources à l’aide du Portail Azure :

1. Ouvrez le portail Azure et accédez à **Tous les services**.
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.
1. Sur la page **Abonnements**, sélectionnez **Fournisseurs de ressources** sous **Paramètres**.
1. Entrez **sql** dans le filtre pour afficher les fournisseurs de ressources liées à SQL.
1. Sélectionnez **Inscrire**, **Réinscrire** ou **Désinscrire** pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée.

   ![Modifier le fournisseur](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

### <a name="command-line"></a>Ligne de commande

Inscrivez votre abonnement Azure auprès du fournisseur **Microsoft.SqlVirtualMachine** en utilisant Azure CLI ou Azure PowerShell.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Inscrivez votre abonnement auprès du fournisseur de ressources à l’aide d’Azure CLI :

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Inscrivez votre abonnement auprès du fournisseur de ressources à l’aide d’Azure PowerShell :

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="full-mode"></a>Mode complet

Il est possible d’inscrire votre machine virtuelle SQL Server directement en mode complet à l’aide d’Azure CLI et Azure PowerShell ou de mettre à niveau le mode léger en mode complet à l’aide du Portail Azure, Azure CLI ou Azure PowerShell. La mise à niveau des machines virtuelles en mode _sans agent_ n’est pas pris en charge avant la mise à niveau du système d’exploitation vers Windows 2008 R2 et version ultérieure.  

À compter de septembre 2021, l’inscription auprès de la machine virtuelle SQL Server en mode complet ne nécessite plus le redémarrage du service SQL Server. 

Pour en savoir plus sur le mode complet, consultez [modes de gestion](sql-server-iaas-agent-extension-automate-management.md#management-modes).

### <a name="register-in-full-mode"></a>S’inscrire en mode complet

Indiquez une licence SQL Server de type paiement à l’utilisation (`PAYG`) pour payer en fonction de l’utilisation, Azure Hybrid Benefit (`AHUB`) pour utiliser votre propre licence ou récupération d’urgence (`DR`) pour activer la [licence de réplica de récupération d’urgence gratuite](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Inscrire une machine virtuelle SQL Server en mode complet avec l’interface de ligne de commande Azure :

```azurecli-interactive
# Register Enterprise or Standard self-installed VM in Lightweight mode
az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> --sql-mgmt-type Full
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Inscrire une machine virtuelle SQL Server en mode COMPLET avec Azure PowerShell :

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
-LicenseType <license_type> -SqlManagementType Full
```

---


### <a name="upgrade-to-full"></a>Mettre à niveau vers le mode complet

Les machines virtuelles SQL Server qui ont inscrit l’extension en mode *léger* peuvent effectuer une mise à niveau vers le mode _complet_ à l’aide du portail Azure, de l’interface de ligne de commande Azure ou d’Azure PowerShell. Les machines virtuelles SQL Server en mode _sans agent_ peuvent passer en mode _complet_ une fois que le système d’exploitation est mis à niveau vers Windows 2008 R2 et versions ultérieures. Il est impossible de passer à une version inférieure. Pour ce faire, vous devez [désinscrire](#unregister-from-extension) la machine virtuelle SQL Server de l’extension SQL IaaS Agent. Cette opération supprime la _ressource_ de **machine virtuelle SQL**, mais ne supprime pas la machine virtuelle elle-même.

#### <a name="azure-portal"></a>Portail Azure

Mettez à niveau l’extension en mode complet avec le Portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à votre ressource [machines virtuelles SQL](manage-sql-vm-portal.md#access-the-resource).
1. Sélectionnez votre machine virtuelle SQL Server et accédez à la page **Vue d’ensemble**.
1. Pour les machines virtuelles SQL Server avec les modes d’extension IaaS NoAgent ou léger, sélectionnez le message **Seules les mises à jour de type de licence et d’édition sont disponibles avec le mode d’extension IaaS SQL...** .

   ![Sélections de changement de mode à partir du portail](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Sélectionnez **Confirmer** pour mettre à niveau votre mode d’extension IaaS SQL Server sur full.

  ![Sélectionnez **Confirmer** pour mettre à niveau votre mode d’extension IaaS SQL Server sur full.](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

#### <a name="command-line"></a>Ligne de commande

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Mettez à niveau l’extension en mode complet avec Azure CLI :

```azurecli-interactive
# Update to full mode
az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Mettez à niveau l’extension en mode complet avec Azure PowerShell : 

```powershell-interactive
# Get the existing  Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -Location $vm.Location
```

---

## <a name="lightweight-mode"></a>Mode léger

Utilisez Azure CLI ou Azure PowerShell pour inscrire votre machine virtuelle SQL Server avec l’extension en mode léger pour des fonctionnalités limitées. 

Indiquez une licence SQL Server de type paiement à l’utilisation (`PAYG`) pour payer en fonction de l’utilisation, Azure Hybrid Benefit (`AHUB`) pour utiliser votre propre licence ou récupération d’urgence (`DR`) pour activer la [licence de réplica de récupération d’urgence gratuite](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Les instances de cluster de basculement et les machines virtuelles SQL Server multi-instances ne peuvent être inscrites avec l’extension Agent IaaS SQL qu’en mode léger.

Pour en savoir plus sur le mode léger, consultez [modes de gestion](sql-server-iaas-agent-extension-automate-management.md#management-modes).

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

## <a name="noagent-mode"></a>Mode sans agent

Les instances de SQL Server 2008 et 2008 R2 installées sur Windows Server 2008 (_non R2_) peuvent être inscrites auprès de l’extension SQL IaaS Agent en [mode NoAgent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Cette option garantit la conformité et permet de surveiller la machine virtuelle SQL Server dans le portail Azure avec des fonctionnalités limitées.

Pour le **type de licence**, spécifiez `AHUB`, `PAYG` ou `DR`. Pour l’**offre d’image**, spécifiez `SQL2008-WS2008` ou `SQL2008R2-WS2008`.

Utilisez Azure CLI ou Azure PowerShell pour inscrire votre instance SQL Server 2008 (`SQL2008-WS2008`) ou 2008 R2 (`SQL2008R2-WS2008`) sur votre machine virtuelle Windows Server 2008. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Inscrire votre machine virtuelle SQL Server en mode sans agent avec l’interface de ligne de commande Azure :

```azurecli-interactive
az sql vm create -n sqlvm -g myresourcegroup -l eastus |
--license-type <license type>  --sql-mgmt-type NoAgent 
--image-sku Enterprise --image-offer <image offer> 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Inscrire votre machine virtuelle SQL Server en mode sans agent avec Azure PowerShell :

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
```

---


## <a name="check-management-mode"></a>Vérifier le mode d’administration

Utilisez Azure PowerShell pour vérifier le mode de gestion dans lequel se trouve votre extension de l’agent IaaS SQL Server. 

Vérifiez le mode de l’extension avec Azure PowerShell :  

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```


## <a name="verify-registration-status"></a>Vérifier l’état de l’inscription

Vous pouvez vérifier si votre machine virtuelle SQL Server a déjà été inscrite auprès de l’extension SQL IaaS Agent via le portail Azure, l’interface de ligne de commande Azure ou Azure PowerShell.

### <a name="azure-portal"></a>Portail Azure

Vérifiez l’état de l’inscription avec le Portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à vos [machines virtuelles SQL Server](manage-sql-vm-portal.md).
1. Sélectionnez votre machine virtuelle SQL Server dans la liste. Si votre machine virtuelle SQL Server n’est pas listée ici, il est probable qu’elle n’a pas été inscrite auprès de l’extension SQL IaaS Agent.
1. Examinez la valeur sous **État**. Si **État** indique **Réussi**, la machine virtuelle SQL Server a bien été inscrite auprès de l’extension SQL IaaS Agent.

   ![Vérifier l’état avec l’inscription avec le fournisseur de ressources SQL](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

Vous pouvez également vérifier l’état en sélectionnant **Réparer** dans le volet **Support + dépannage** de la ressource **Machine virtuelle SQL**. L’état de provisionnement de l’extension de l’agent IaaS SQL peut être **Réussite** ou **Échec**. 

### <a name="command-line"></a>Ligne de commande

Vérifiez l’état d’inscription actuel d’une machine virtuelle SQL Server à l’aide d’Azure CLI ou d’Azure PowerShell. `ProvisioningState` affichera `Succeeded` si l’inscription a réussi.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Vérifiez l’état de l’inscription avec Azure CLI : 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Vérifiez l’état de l’inscription avec Azure PowerShell : 

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Une erreur indique que la machine virtuelle SQL Server n’a pas été inscrite auprès de l’extension.

## <a name="repair-extension"></a>Réparer l’extension

Il est possible que votre extension de l’agent IaaS SQL soit dans un état d’échec. Utilisez le portail Azure pour réparer l’extension de l’agent IaaS SQL. 

Pour réparer l’extension avec le Portail Azure :  

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à vos [machines virtuelles SQL Server](manage-sql-vm-portal.md).
1. Sélectionnez votre machine virtuelle SQL Server dans la liste. Si votre machine virtuelle SQL Server n’est pas listée ici, il est probable qu’elle n’a pas été inscrite auprès de l’extension SQL IaaS Agent.
1. Sélectionnez **Réparer** sous **Support + dépannage** dans la page de la ressource **Machine virtuelle SQL**. 

   :::image type="content" source="media/sql-agent-extension-manually-register-single-vm/repair-extension.png" alt-text="Sélectionnez **Réparer** sous **Support + dépannage** dans la page de la ressource **Machine virtuelle SQL**":::   

1. Si votre état de provisionnement indique **Échec**, sélectionnez **Réparer** pour réparer l’extension. Si votre état est **Réussite**, vous pouvez cocher la case en regard de **Forcer la réparation** pour réparer l’extension, quel que soit l’état. 

   ![Si votre état de provisionnement indique **Échec**, sélectionnez **Réparer** pour réparer l’extension. Si votre état est **Réussite**, vous pouvez cocher la case en regard de **Forcer la réparation** pour réparer l’extension, quel que soit l’état.](./media/sql-agent-extension-manually-register-single-vm/force-repair-extension.png)


## <a name="unregister-from-extension"></a>Désinscrire de l’extension

Pour désinscrire votre machine virtuelle SQL Server de l’extension SQL IaaS Agent, supprimez la *ressource* de machine virtuelle SQL à l’aide du portail Azure ou de l’interface de ligne de commande Azure. La suppression de la *ressource* de machine virtuelle SQL n’entraîne pas la suppression de la machine virtuelle SQL Server proprement dite. La désinscription de la machine virtuelle SQL de l’extension SQL IaaS Agent est nécessaire pour passer du mode d’administration complet à une version inférieure.

>[!CAUTION]
> **Soyez extrêmement vigilant** lors de l’annulation de l’inscription de votre machine virtuelle SQL Server à partir de l’extension. Suivez attentivement les étapes, car **il est possible de supprimer par inadvertance la machine virtuelle** lors de la tentative de suppression de la *ressource*.


### <a name="azure-portal"></a>Portail Azure

Pour désinscrire votre machine virtuelle SQL Server de l’extension à l’aide du portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à la ressource de machine virtuelle SQL.

   ![Ressource Machines virtuelles SQL](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Sélectionnez **Supprimer**.

   ![Sélectionnez Supprimer dans la barre de navigation supérieure](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Saisissez le nom de la machine virtuelle SQL et **désactivez la case à cocher à côte de celle-ci**.

   ![Décochez la machine virtuelle pour empêcher la suppression de la machine virtuelle réelle, puis sélectionnez Supprimer pour procéder à la suppression de la ressource de machine virtuelle SQL.](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   > [!WARNING]
   > Si vous ne désactivez pas la case à cocher en regard du nom de la machine virtuelle, vous *supprimez entièrement* celle-ci. Désactivez la case à cocher pour désinscrire la machine virtuelle SQL Server de l’extension *sans supprimer la machine virtuelle proprement dite*.

1. Sélectionnez **Supprimer** pour confirmer la suppression de la *ressource* de machine virtuelle SQL et non pas la machine virtuelle SQL Server.

### <a name="command-line"></a>Ligne de commande

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour désinscrire votre machine virtuelle SQL Server de l’extension à l’aide d’Azure CLI, utilisez la commande [az sql vm delete](/cli/azure/sql/vm#az_sql_vm_delete). Cela supprime la *ressource* de la machine virtuelle SQL Server, mais pas la machine virtuelle.

Pour annuler l’inscription de votre machine virtuelle SQL Server avec Azure CLI : 

```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour désinscrire votre machine virtuelle SQL Server de l’extension à l’aide d’Azure PowerShell, utilisez la commande [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Cela supprime la *ressource* de la machine virtuelle SQL Server, mais pas la machine virtuelle.

Pour annuler l’inscription de votre machine virtuelle SQL Server avec Azure PowerShell : 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <SQL VM resource name>
```

---

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Questions fréquentes (FAQ) pour SQL Server sur une machine virtuelle Windows](frequently-asked-questions-faq.yml)
* [Guide des prix de SQL Server sur machines virtuelles Azure](../windows/pricing-guidance.md)
* [Nouveautés de SQL Server sur des machines virtuelles Azure](../windows/doc-changes-updates-release-notes-whats-new.md)
