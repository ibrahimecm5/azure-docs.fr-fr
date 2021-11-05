---
title: Extension SQL Server IaaS Agent pour Linux
description: Cet article explique comment l’extension SQL Server IaaS Agent permet d’automatiser la gestion des tâches d’administration spécifiques des machines virtuelles Azure SQL Server sur Linux.
services: virtual-machines-windows
documentationcenter: ''
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.openlocfilehash: 98e448358d31fda34a8bb84024be6daf157d0f78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098039"
---
# <a name="sql-server-iaas-agent-extension-for-linux"></a>Extension SQL Server IaaS Agent pour Linux
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-iaas-agent-extension-automate-management.md)
> * [Linux](sql-server-iaas-agent-extension-linux.md)

L’extension SQL Server IaaS Agent (SqlIaasExtension) s’exécute sur des machines virtuelles Azure SQL Server sur Linux pour automatiser les tâches d’administration et de gestion. 

Cet article fournit une vue d’ensemble de l’extension. Pour en savoir plus, consultez [Inscrire à l’extension](sql-iaas-agent-extension-register-vm-linux.md). 


## <a name="overview"></a>Vue d’ensemble

L’extension SQL Server IaaS Agent permet l’intégration au portail Azure et offre les avantages suivants pour les machines virtuelles Azure SQL Server sur Linux : 

- **Conformité** : L’extension offre une méthode simplifiée pour répondre au besoin de notifier Microsoft qu’Azure Hybrid Benefit a été activé, tel que spécifié dans les conditions d’utilisation du produit. Ce processus élimine la nécessité de gérer les formulaires d’inscription de licence pour chaque ressource.  

- **Gestion simplifiée des licences** : L’extension simplifie la gestion des licences SQL Server et vous permet d’identifier rapidement les machines virtuelles SQL Server avec Azure Hybrid Benefit activé, à l’aide du portail Azure, d’Azure PowerShell ou d’Azure CLI : 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---

- **Gratuit** : Aucun coût supplémentaire n’est associé à l’extension. 



## <a name="installation"></a>Installation

[Inscrivez](sql-iaas-agent-extension-register-vm-linux.md) votre machine virtuelle SQL Server avec l’extension SQL Server IaaS Agent pour créer la **ressource** de _machine virtuelle SQL_ dans votre abonnement, qui est une ressource _distincte_ de la ressource de machine virtuelle. Le fait de désinscrire votre machine virtuelle SQL Server de l’extension va supprimer la _ressource_ de **machine virtuelle SQL** de votre abonnement, mais pas la machine virtuelle elle-même.

Actuellement, l’extension SQL Server IaaS Agent pour Linux est disponible en mode allégé uniquement. 


## <a name="verify-extension-status"></a>Vérifier l’état de l’extension

Utilisez le portail Azure ou Azure PowerShell pour vérifier l’état de l’extension. 

### <a name="azure-portal"></a>Portail Azure

Vérifiez que l’extension est installée en utilisant le portail Azure. 

Accédez à votre ressource de **machine virtuelle** dans le portail Azure (pas la ressource de *machines virtuelles SQL*, mais la ressource pour votre machine virtuelle). Sélectionnez **Extensions** sous **Paramètres**. Vous devriez voir l’extension **SslIaaSExtension**, comme dans l’exemple suivant : 

![Vérifier l’état de l’extension SQL Server IaaS Agent SqlIaaSExtension dans le portail Azure](../windows/media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)




### <a name="azure-powershell"></a>Azure PowerShell

Vous pouvez également utiliser la cmdlet Azure PowerShell **Get-AzVMSqlServerExtension** :

```powershell-interactive
  Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
```

La commande précédente confirme que l’agent est installé et fournit des informations générales sur son état. Vous pouvez obtenir des informations d’état sur la sauvegarde automatisée et la mise à jour corrective automatisée à l’aide des commandes suivantes :

```powershell-interactive
 $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
 $sqlext.AutoPatchingSettings
 $sqlext.AutoBackupSettings
```

## <a name="limitations"></a>Limites

L’extension SQL IaaS Agent pour Linux a les limitations suivantes : 

- Seules les machines virtuelles SQL Server s’exécutant sur le système d’exploitation Ubuntu Linux sont prises en charge. Les autres distributions Linux ne sont pas prises en charge actuellement.
- Les machines virtuelles SQL Server s’exécutant sur Ubuntu Linux Pro ne sont pas prises en charge.
- Les machines virtuelles SQL Server s’exécutant sur des images généralisées ne sont pas prises en charge.
- Seules les machines virtuelles SQL Server déployées par le biais d’Azure Resource Manager sont prises en charge. Les machines virtuelles SQL Server déployées via le modèle classique ne sont pas prises en charge. 
- SQL Server avec une seule instance. Pas de prise en charge s’il y a plusieurs instances. 

## <a name="privacy-statement"></a><a id="in-region-data-residency"></a> Déclaration de confidentialité

Lorsque vous utilisez SQL Server sur des machines virtuelles Azure et l’extension SQL IaaS, prenez en compte des déclarations de confidentialité suivantes : 

- **Collecte de données** : L’extension SQL IaaS Agent collecte des données dans le seul but de fournir d’autres avantages aux clients lors de l’utilisation de SQL Server sur des machines virtuelles Azure. Microsoft **n’utilisera pas ces données pour les audits de licences** sans le consentement préalable du client. Pour plus d’informations, consultez l’[Avenant à la déclaration de confidentialité de SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

- **Résidence des données dans la région** : SQL Server sur les machines virtuelles Azure et l’extension SQL Server IaaS Agent ne déplacent ni ne stockent les données client hors de la région dans laquelle les machines virtuelles sont déployées. 


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, consultez [Qu’est-ce que SQL Server sur les machines virtuelles Azure Linux ?](sql-server-on-linux-vm-what-is-iaas-overview.md).

Pour en savoir plus, consultez la [Foire aux questions](frequently-asked-questions-faq.yml).
