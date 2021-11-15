---
title: Utiliser des outils en ligne de commande pour démarrer et arrêter des machines virtuelles
description: Découvrez comment utiliser les outils de ligne de commande pour démarrer et arrêter des machines virtuelles dans Azure DevTest Labs.
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 58440d00f888816f95aac0159063a7b6d6fc5289
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430188"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Utiliser des outils de ligne de commande pour démarrer et arrêter des machines virtuelles Azure DevTest Labs

Cet article explique comment démarrer ou arrêter des machines virtuelles de labo dans Azure DevTest Labs. Vous pouvez créer des scripts Azure PowerShell ou Azure CLI pour automatiser ces opérations. 

## <a name="prerequisites"></a>Prérequis
- Si vous utilisez PowerShell, le [module Az](/powershell/azure/new-azureps-module-az) doit être installé sur votre station de travail. Vérifiez que vous avez la version la plus récente. Si nécessaire, exécutez `Update-Module -Name Az`.

- Si vous voulez utiliser Azure CLI et que vous ne l’avez pas encore installé, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

- Une machine virtuelle dans un labo DevTest Labs.

## <a name="overview"></a>Vue d’ensemble

Azure DevTest Labs permet de créer des environnements dev/test rapides, simples et légers. Les labos vous permettent de gérer les coûts, de créer rapidement des machines virtuelles et de réduire le gaspillage. Vous pouvez utiliser les fonctionnalités dans le Portail Azure pour démarrer et arrêter automatiquement des machines virtuelles à des moments précis. Toutefois, vous souhaiterez peut-être automatiser le démarrage et l’arrêt des machines virtuelles à partir de scripts. Voici des situations dans lesquelles il pourrait être utile d’exécuter ces tâches à l’aide de scripts.

- Lorsque vous utilisez une application à trois niveaux dans un environnement de test et que les niveaux doivent être démarrés dans l’ordre. 
- Pour désactiver une machine virtuelle selon un critère personnalisé pour faire des économies. 
- En tant que tâche dans un flux de travail d’intégration continue et de livraison continue pour démarrer au début, puis arrêter les machines virtuelles une fois le processus terminé. Un bon exemple de ce workflow est la fabrique d’images personnalisées avec Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

Le script PowerShell suivant permet de démarrer ou d’arrêter une machine virtuelle dans un labo. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) est au cœur de ce script. Le paramètre **ResourceId** est l’ID de ressource qualifiée pour la machine virtuelle dans le labo. Le paramètre **Action** permet de définir les options **Démarrer** ou **Arrêter** en fonction des besoins.

1. Depuis votre station de travail, connectez-vous à votre abonnement Azure avec la cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount)PowerShell et suivez les instructions qui s’affichent à l’écran.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. Fournissez une valeur appropriée pour les variables, puis exécutez le script.

    ```powershell
    $devTestLabName = "yourlabname"
    $vMToStart = "vmname"
    
    # The action on the virtual machine (Start or Stop)
    $vmAction = "Start"
    ```

1. Démarrez ou arrêtez la machine virtuelle en fonction de la valeur que vous avez passée à `$vmAction`.

    ```powershell
    # Get the lab information
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName
    
    # Start or stop the VM and return a succeeded or failed status
    $returnStatus = Invoke-AzResourceAction `
                        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                        -Action $vmAction `
                        -Force
    
    if ($returnStatus.Status -eq 'Succeeded') {
        Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
    }
    else {
        Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
    }
    ```

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/get-started-with-azure-cli) est un autre moyen d’automatiser le démarrage et l’arrêt des machines virtuelles DevTest Labs. Le script suivant vous fournit les commandes pour démarrer et arrêter une machine virtuelle dans un labo. L’utilisation de variables dans cette section est basée sur un environnement Windows. De petites modifications seront nécessaires si vous utilisez d’autres environnements.

1. Remplacez `SubscriptionID`, `yourlabname`, `yourVM`, et `action` par les valeurs appropriées. Exécutez ensuite le script.

    ```azurecli
    set SUBSCIPTIONID=SubscriptionID
    set DEVTESTLABNAME=yourlabname
    set VMNAME=yourVM
    
    REM The action on the virtual machine (Start or Stop)
    set ACTION=action
    ```

1. Connectez-vous à votre abonnement Azure et récupérez le nom du groupe de ressources qui contient le labo.

    ```azurecli
    az login
    
    REM If you have multiple subscriptions, set the one to use
    REM az account set --subscription %SUBSCIPTIONID%

    az resource list --resource-type "Microsoft.DevTestLab/labs" --name %DEVTESTLABNAME% --query "[0].resourceGroup"
    ```

1. Remplacez `resourceGroup` par la valeur obtenue lors de l’étape précédente. Exécutez ensuite le script.

    ```azurecli
    set RESOURCEGROUP=resourceGroup
    ```

1. Démarrez ou arrêtez la machine virtuelle en fonction de la valeur que vous avez passée à `ACTION`.

    ```azurecli
    az lab vm %ACTION% --lab-name %DEVTESTLABNAME% --name %VMNAME% --resource-group %RESOURCEGROUP%
    ```

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article suivant qui explique comment utiliser le portail Azure pour effectuer ces opérations : [Redémarrer une machine virtuelle](devtest-lab-restart-vm.md).
