---
title: Appliquer la licence Windows pour machines virtuelles hôtes de session – Azure
description: Décrit comment appliquer la licence Windows pour des machines virtuelles Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 856cefa33948f530ee859ee1eeadfaf2cdf73de6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471779"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Appliquer la licence Windows pour machines virtuelles hôtes de session

Les clients disposant d’une licence appropriée pour exécuter des charges de travail Azure Virtual Desktop sont éligibles pour appliquer une licence Windows à leurs machines virtuelles hôtes de session et les exécuter sans avoir à payer d’autre licence. Pour plus d’informations, voir [Tarification de Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-azure-virtual-desktop-license"></a>Méthodes d’utilisation de votre licence Azure Virtual Desktop
La gestion des licences Azure Virtual Desktop vous permet d’appliquer une licence à toute machine virtuelle Windows ou Windows Server inscrite en tant qu’hôte de session dans un pool d’hôtes, et recevant des connexions utilisateur. Cette licence ne s’applique pas aux machines virtuelles qui s’exécutent en tant que serveurs de partage de fichiers, contrôleurs de domaine, etc.

Il existe plusieurs façons d’utiliser la licence Azure Virtual Desktop :
- Vous pouvez créer un pool d’hôtes et ses machines virtuelles hôtes de session à l’aide de l’[offre de la Place de marché Azure](./create-host-pools-azure-marketplace.md). La licence est automatiquement appliquée aux machines virtuelles créées de cette manière.
- Vous pouvez créer un pool d’hôtes et ses machines virtuelles hôtes de session à l’aide du [Modèle Azure Resource Manager sur GitHub](./virtual-desktop-fall-2019/create-host-pools-arm-template.md). La licence est automatiquement appliquée aux machines virtuelles créées de cette manière.
- Vous pouvez appliquer une licence à une machine virtuelle hôte de session existante. Pour ce faire, suivez d’abord les instructions fournies dans [Créer un pool d’hôtes avec PowerShell](./create-host-pools-powershell.md) afin de créer un pool d’hôtes et les machines virtuelles associées, puis revenez à cet article pour découvrir comment appliquer la licence.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Appliquer une licence Windows à une machine virtuelle hôte de session
Vérifiez que vous avez [installé et configuré la dernière version d’Azure PowerShell](/powershell/azure/). Exécutez la cmdlet PowerShell suivante pour appliquer la licence Windows :

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Vérifier que votre machine virtuelle hôte de session utilise l’avantage des licences
Après avoir déployé votre machine virtuelle, exécutez cette cmdlet pour vérifier le type de licence :
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Une machine virtuelle hôte de session à laquelle la licence Windows est appliquée affiche quelque chose comme ceci :

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Les machines virtuelles auxquelles aucune licence Windows n’est appliquée affichent quelque chose comme ceci :

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Exécutez la cmdlet suivante pour afficher la liste de toutes les machines virtuelles hôtes de session auxquelles la licence Windows est appliquée dans votre abonnement Azure :

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>Configuration requise pour le déploiement des services Bureau à distance de Windows Server

Si vous déployez Windows Server en tant qu’hôte Azure Virtual Desktop dans votre déploiement, un serveur de licences des services Bureau à distance doit être accessible depuis ces machines virtuelles. Le serveur de licences de services Bureau à distance peut être présent localement ou dans Azure. Pour plus d’informations, consultez [Activer le serveur de licences des services Bureau à distance](/windows-server/remote/remote-desktop-services/rds-activate-license-server).
