---
title: Installer l’agent Azure Monitor
description: Options d’installation de l’agent Azure Monitor sur les machines virtuelles Azure et les serveurs avec Azure Arc.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 3c1b3ac13f79037b3357f3ecba0d3d668f88d0b5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850885"
---
# <a name="install-the-azure-monitor-agent"></a>Installer l’agent Azure Monitor
Cet article présente les différentes options actuellement disponibles pour installer l’[agent Azure Monitor](azure-monitor-agent-overview.md) sur les machines virtuelles Azure et les serveurs avec Azure Arc, ainsi que les options permettant de créer des [associations avec des règles de collecte de données](data-collection-rule-azure-monitor-agent.md) qui définissent les données que l’agent doit collecter.

## <a name="prerequisites"></a>Prérequis
Les conditions préalables suivantes sont requises avant l’installation de l’agent Azure Monitor.

- L’[identité du système managé](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) doit être activée sur les machines virtuelles Azure. Cela n’est pas nécessaire pour les serveurs avec Azure Arc. L’identité système est activée automatiquement si l’agent est installé dans le cadre du processus de [création et d’affectation d’une règle de collecte de données à l’aide du portail Azure](#install-with-azure-portal).
- L’[étiquette de service AzureResourceManager](../../virtual-network/service-tags-overview.md) doit être activée sur le réseau virtuel pour la machine virtuelle.
- La machine virtuelle doit avoir accès aux points de terminaison HTTPS suivants :
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com

> [!IMPORTANT]
> L’agent Azure Monitor ne prend actuellement pas en charge les liaisons privées.

## <a name="virtual-machine-extension-details"></a>Détails de l’extension de machine virtuelle
L’agent Azure Monitor est implémenté en tant qu’[extension de machine virtuelle Azure](../../virtual-machines/extensions/overview.md) avec les détails dans le tableau suivant. Il peut être installé à l’aide de l’une des méthodes d’installation des extensions de machine virtuelle, notamment celles décrites dans cet article.

| Propriété | Windows | Linux |
|:---|:---|:---|
| Serveur de publication | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| Type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

## <a name="extension-versions"></a>Versions d’extension
Il est vivement recommandé d’effectuer la mise à jour vers les versions GA et ultérieures listées ci-dessous au lieu d’utiliser la préversion ou les versions intermédiaires.

| Date de sortie | Notes de publication | Windows | Linux |
|:---|:---|:---|:---|:---|
| Juin 2021 | Disponibilité générale annoncée. <ul><li>Toutes les fonctionnalités à l’exception de la destination des métriques sont désormais en disponibilité générale</li><li>Qualité de production, sécurité et conformité</li><li>Disponibilité dans toutes les régions publiques</li><li>Améliorations des performances et de la mise à l’échelle pour l’EPS le plus élevé</li></ul> [En savoir plus](https://azure.microsoft.com/updates/azure-monitor-agent-and-data-collection-rules-now-generally-available/) | 1.0.12.0 | 1.9.1.0 |
| Juillet 2021 | <ul><li>Prise en charge des proxies directs</li><li>Prise en charge de la passerelle Log Analytics</li></ul> [En savoir plus](https://azure.microsoft.com/updates/general-availability-azure-monitor-agent-and-data-collection-rules-now-support-direct-proxies-and-log-analytics-gateway/) | 1.1.1.0 | 1.10.5.0 |
| Août 2021 | Résolution du problème autorisant les métriques Azure Monitor comme seule destination | 1.1.2.0 | 1.10.9.0<sup>1</sup> |
| Septembre 2021 | <ul><li>Résolution du problème à l’origine de la perte de données lors du redémarrage de l’agent</li><li>Résolution d’une régression introduite dans 1.1.3.1<sup>2</sup> pour les serveurs Windows Arc</li></ul> | 1.1.3.2 | 1.12.2.0 <sup>2</sup> |  

<sup>1</sup> N’utilisez pas AMA pour Linux version 1.10.7.0 <sup>2</sup> Régression connue empêchant le fonctionnement sur les serveurs avez Arc


## <a name="install-with-azure-portal"></a>Installer avec Portail Azure
Pour installer l’agent Azure Monitor à l’aide du portail Azure, suivez le processus de [création d’une règle de collecte de données](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) dans le portail Azure. Cela vous permet d’associer la règle de collecte de données à au moins une machine virtuelle Azure ou à des serveurs avec Azure Arc. L’agent est installé sur l’une de ces machines virtuelles sur laquelle il n’est pas encore installé.


## <a name="install-with-resource-manager-template"></a>Installer avec un modèle Resource Manager
Vous pouvez utiliser des modèles Resource Manager pour installer l’agent Azure Monitor sur des machines virtuelles Azure et sur des serveurs avec Azure Arc, ainsi que pour créer une association avec des règles de collecte de données. Vous devez créer une règle de collecte des données avant de créer l’association.

Obtenez des exemples de modèles pour l’installation de l’agent et la création de l’association à partir des éléments suivants : 

- [Modèle d’installation de l’agent Azure Monitor (Azure et Azure Arc)](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Modèle de création d’une association avec une règle de collecte de données](./resource-manager-data-collection-rules.md)

Installez les modèles à l’aide de [n’importe quelle méthode de déploiement pour les modèles Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md) comme les commandes suivantes.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Installer avec PowerShell
Vous pouvez installer l’agent Azure Monitor sur des machines virtuelles Azure et sur des serveurs avec Azure Arc à l’aide de la commande PowerShell pour ajouter une extension de machine virtuelle. 

### <a name="azure-virtual-machines"></a>Machines virtuelles Azure
Utilisez les commandes PowerShell suivantes pour installer l’agent Azure Monitor sur des machines virtuelles Azure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5
```
---

### <a name="azure-arc-enabled-servers"></a>Serveurs avec Azure Arc
Utilisez les commandes PowerShell suivantes pour installer l’agent Azure Monitor sur des serveurs avec Azure Arc.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
---
## <a name="install-with-azure-cli"></a>Installer avec Azure CLI
Vous pouvez installer l’agent Azure Monitor sur des machines virtuelles Azure et sur des serveurs Azure Arc à l’aide de la commande Azure CLI pour ajouter une extension de machine virtuelle. 

### <a name="azure-virtual-machines"></a>Machines virtuelles Azure
Utilisez les commandes CLI suivantes pour installer l’agent Azure Monitor sur des machines virtuelles Azure.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Serveurs avec Azure Arc
Utilisez les commandes CLI suivantes pour installer l’agent Azure Monitor sur des serveurs avec Azure Arc.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorWindowsAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorLinuxAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
---


## <a name="install-with-azure-policy"></a>Installer avec Azure Policy
Utilisez les stratégies et les initiatives de stratégie suivantes pour installer automatiquement l’agent et l’associer à une règle de collecte de données, chaque fois que vous créez une machine virtuelle.

### <a name="built-in-policy-initiatives"></a>Initiatives de stratégie intégrées
[Consultez les prérequis de l’installation de l’agent](azure-monitor-agent-install.md#prerequisites). 

Il existe des initiatives de stratégie pour les machines virtuelles Windows et Linux comportant des stratégies individuelles qui :

- Installent l’extension de l’agent Azure Monitor sur la machine virtuelle.
- Créent et déploient l’association pour lier la machine virtuelle à une règle de collecte des données.

![Capture d’écran partielle des définitions Azure Policy illustrant deux initiatives de stratégie intégrée pour la configuration de l’agent Azure Monitor.](media/azure-monitor-agent-install/built-in-ama-dcr-initiatives.png)  

### <a name="built-in-policies"></a>Stratégies prédéfinies 
Vous pouvez choisir d’utiliser les stratégies individuelles venant de leurs initiatives de stratégie respectives, en fonction de vos besoins. Par exemple, si vous ne souhaitez installer automatiquement que l’agent, utilisez la première stratégie de l’initiative, comme dans l’exemple ci-dessous.  

![Capture d’écran partielle de la page des définitions Azure Policy illustrant les stratégies contenues dans l’initiative pour la configuration de l’agent Azure Monitor.](media/azure-monitor-agent-install/built-in-ama-dcr-policy.png)  

### <a name="remediation"></a>Correction
Les initiatives ou les stratégies s’appliquent à chaque machine virtuelle au moment où elle est créée. Une [tâche de correction](../../governance/policy/how-to/remediate-resources.md) déploie les définitions de stratégie dans l’initiative sur les *ressources existantes*, ce qui vous permet de configurer l’agent Azure Monitor pour toutes les ressources qui ont déjà été créées. 

Quand vous créez l’affectation à partir du portail Azure, vous avez la possibilité de créer une tâche de correction simultanément. Pour plus d’informations sur la correction, consultez [Corriger les ressources non conformes avec Azure Policy](../../governance/policy/how-to/remediate-resources.md).

![Capture d’écran montrant la mise à jour de l’initiative pour l’agent Azure Monitor.](media/azure-monitor-agent-install/built-in-ama-dcr-remediation.png)

## <a name="diagnostic-settings"></a>Paramètres de diagnostic
Les [paramètres de diagnostic](../essentials/diagnostic-settings.md) collectent les journaux de ressources et les métriques à partir des ressources Azure et les acheminent vers plusieurs emplacements. Un emplacement standard est un espace de travail Log Analytics qui vous permet d’analyser les données avec les [requêtes de journal](../logs/log-query-overview.md) et les [alertes de journal](../alerts/alerts-log.md). Utilisez Azure Policy pour créer automatiquement un paramètre de diagnostic chaque fois que vous créez une ressource.


## <a name="next-steps"></a>Étapes suivantes

- [Créez une règle de collecte de données](data-collection-rule-azure-monitor-agent.md) pour collecter des données à partir de l’agent et les envoyer à Azure Monitor.
