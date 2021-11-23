---
title: Démarrer des machines à l’aide de runbooks Automation
description: Découvrez comment démarrer des machines virtuelles dans un labo dans Azure DevTest Labs en utilisant des runbooks Azure Automation.
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24a9b21e9a081a875653a6b368f1ce501b1cb0fb
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401893"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Démarrer des machines virtuelles dans un labo dans un certain ordre en utilisant des runbooks Azure Automation
La fonctionnalité de [démarrage automatique](devtest-lab-set-lab-policy.md#set-autostart) de DevTest Labs vous permet configurer des machines virtuelles pour qu’elles démarrent automatiquement à une heure spécifiée. Cependant, cette fonctionnalité ne prend pas en charge le démarrage des machines dans un ordre spécifique. Il existe plusieurs scénarios où ce type d’automatisation peut être utile.  Un scénario est l’emplacement où une machine virtuelle JumpBox dans un labo est le point d’accès aux autres machines virtuelles. La machine virtuelle JumpBox doit démarrer avant les autres machines virtuelles. Cet article vous montre comment configurer un compte Azure Automation avec un runbook PowerShell qui exécute un script. Le script utilise des étiquettes sur les machines virtuelles du labo pour vous permettre de contrôler l’ordre de démarrage sans devoir modifier le script.

## <a name="setup"></a>Programme d’installation
Dans cet exemple, l’étiquette **StartupOrder** avec la valeur appropriée, comme 0, 1 ou 2, doit être ajoutée aux machines virtuelles du labo. Désignez les machines qui n’ont pas besoin de démarrer avec la valeur -1.

## <a name="create-an-azure-automation-account"></a>Créer un compte Azure Automation
Créez un compte Azure Automation en suivant les instructions de [cet article](../automation/automation-create-standalone-account.md). Choisissez l’option **Compte d’identification** lors de la création du compte. Une fois que le compte Automation est créé, ouvrez la page **Modules**, puis sélectionnez **Mettre à jour les modules Azure** sur la barre de menus. Les modules par défaut sont dans des versions plus anciennes ; le script ne peut pas fonctionner sans la mise à jour.

## <a name="add-a-runbook"></a>Ajouter un runbook
Maintenant, pour ajouter un runbook au compte Automation, sélectionnez **Runbooks** sur le menu de gauche. Sélectionnez **Ajouter un runbook** sur le menu, puis suivez les instructions pour [créer un runbook PowerShell](../automation/learn/powershell-runbook-managed-identity.md).

## <a name="powershell-script"></a>Script PowerShell
Le script suivant prend comme paramètres le nom de l’abonnement et le nom du labo. Le flux du script est destiné à obtenir toutes les machines virtuelles dans le labo, puis d’analyser les informations des étiquettes pour créer une liste des noms des machines virtuelles et de leur ordre de démarrage. Le script parcourt les machines virtuelles dans l’ordre et les démarre. S’il existe plusieurs machines virtuelles avec un même numéro d’ordre, elles démarrent de façon asynchrone via des travaux PowerShell. Pour les machines virtuelles qui n’ont pas de balise, définissez la valeur de démarrage sur la dernière (10). Par défaut, ces machines démarrent en dernier.  Si vous ne souhaitez pas que la machine virtuelle soit démarrée automatiquement, définissez la valeur de balise sur 11 et le script ignorera la machine virtuelle.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Créer une planification
Pour que ce script s’exécute tous les jours, [créez une planification](../automation/shared-resources/schedules.md#create-a-schedule) dans le compte Automation. Une fois que la planification est créée, [liez-la au runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

Dans une situation à grande échelle qui a plusieurs abonnements avec plusieurs labos, stockez les informations de paramètres dans un fichier pour les différents labos. Passez le fichier au script au lieu de passer les paramètres individuels. Le script doit être modifié, mais l’exécution est globalement la même. Bien que cet exemple utilise Azure Automation pour exécuter le script PowerShell, il existe d’autres options, comme l’utilisation d’une tâche dans un pipeline de build/mise en production.

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant pour en savoir plus sur Azure Automation : [Présentation d’Azure Automation](../automation/automation-intro.md).