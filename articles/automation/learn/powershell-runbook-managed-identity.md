---
title: Créer un runbook PowerShell à l’aide d’une identité managée dans Azure Automation
description: Dans ce tutoriel, vous allez apprendre à utiliser des identités managées avec un runbook PowerShell dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: tutorial
ms.openlocfilehash: 31f9e30d938f70852eac5c5185626828e5305897
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427168"
---
# <a name="tutorial-create-automation-powershell-runbook-using-managed-identity"></a>Tutoriel : Créer un runbook PowerShell Automation avec une identité managée

Ce tutoriel vous guide dans la création d’un [runbook PowerShell](../automation-runbook-types.md#powershell-runbooks) dans Azure Automation qui utilise une [identité managée](../automation-security-overview.md#managed-identities), plutôt que le compte d’identification, pour interagir avec les ressources. Les Runbooks PowerShell sont basés sur Windows PowerShell. Une identité managée issue d’Azure Active Directory (Azure AD) permet à votre runbook d’accéder facilement aux autres ressources protégées par Azure AD.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Attribuer des autorisations aux identités managées
> * Créer un runbook PowerShell

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure Automation avec au moins une identité managée affectée par l’utilisateur. Pour plus d’informations, consultez [Utilisation d’une identité managée affectée par l’utilisateur pour un compte Azure Automation](../add-user-assigned-identity.md).
* Modules Az : `Az.Accounts`, `Az.Automation`, `Az.ManagedServiceIdentity` et `Az.Compute` importés dans le compte Automation. Pour plus d’informations, consultez [Importer des modules Az](../shared-resources/modules.md#import-az-modules).
* Le [module PowerShell Azure Az](/powershell/azure/new-azureps-module-az) installé sur votre ordinateur. Pour plus d'informations sur son installation ou sa mise à niveau, consultez [Installer le module PowerShell Azure Az](/powershell/azure/install-az-ps). `Az.ManagedServiceIdentity` est un module en préversion qui n’est pas installé dans le cadre du module Az. Pour l’installer, exécutez `Install-Module -Name Az.ManagedServiceIdentity`.
* Une [machine virtuelle Azure](../../virtual-machines/windows/quick-create-powershell.md). Comme vous allez devoir l’arrêter et la démarrer, il ne doit pas s’agir d’une machine virtuelle de production.
* Une connaissance générale des [runbooks Automation](../manage-runbooks.md).

## <a name="assign-permissions-to-managed-identities"></a>Attribuer des autorisations aux identités managées

Affectez des autorisations aux identités managées pour leur permettre d’arrêter et de démarrer une machine virtuelle.

1. Connectez-vous à Azure de manière interactive à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) et suivez les instructions.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. Fournissez une valeur appropriée pour les variables ci-dessous, puis exécutez le script.

    ```powershell
    $resourceGroup = "resourceGroupName"
    
    # These values are used in this tutorial
    $automationAccount = "xAutomationAccount"
    $userAssignedManagedIdentity = "xUAMI"
    ```

1. Utilisez la cmdlet PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) pour affecter un rôle à l’identité managée affectée par le système.

    ```powershell
    $role1 = "DevTest Labs User"
    
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. La même attribution de rôle est nécessaire pour l’identité managée affectée par l’utilisateur

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. Des autorisations supplémentaires pour l’identité gérée affectée par le système sont nécessaires pour exécuter les cmdlets `Get-AzUserAssignedIdentity` et `Get-AzAutomationAccount` comme dans ce tutoriel.

    ```powershell
    $role2 = "Reader"
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role2
    ```

## <a name="create-powershell-runbook"></a>Mon premier Runbook PowerShell

Créez un runbook qui autorise l’exécution par l’une des identités managées. Le runbook démarre une machine virtuelle arrêtée ou arrête une machine virtuelle en cours d’exécution.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre compte Automation.

1. Sous **Automatisation de processus**, sélectionnez **Runbooks**.

1. Sélectionnez **Créer un runbook**.
    1. Nommez le runbook `miTesting`.
    1. Dans le menu déroulant **Type de runbook**, sélectionnez **PowerShell**.
    1. Sélectionnez **Create** (Créer).

1. Dans l’éditeur de runbook, collez le code suivant :

    ```powershell
    Param(
     [string]$resourceGroup,
     [string]$VMName,
     [string]$method,
     [string]$UAMI 
    )
    
    $automationAccount = "xAutomationAccount"
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process | Out-Null
    
    # Connect using a Managed Service Identity
    try {
            $AzureContext = (Connect-AzAccount -Identity).context
        }
    catch{
            Write-Output "There is no system-assigned user identity. Aborting."; 
            exit
        }
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
        -DefaultProfile $AzureContext
    
    if ($method -eq "SA")
        {
            Write-Output "Using system-assigned managed identity"
        }
    elseif ($method -eq "UA")
        {
            Write-Output "Using user-assigned managed identity"
    
            # Connects using the Managed Service Identity of the named user-assigned managed identity
            $identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
                -Name $UAMI -DefaultProfile $AzureContext
    
            # validates assignment only, not perms
            if ((Get-AzAutomationAccount -ResourceGroupName $resourceGroup `
                    -Name $automationAccount `
                    -DefaultProfile $AzureContext).Identity.UserAssignedIdentities.Values.PrincipalId.Contains($identity.PrincipalId))
                {
                    $AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context
    
                    # set and store context
                    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
                }
            else {
                    Write-Output "Invalid or unassigned user-assigned managed identity"
                    exit
                }
        }
    else {
            Write-Output "Invalid method. Choose UA or SA."
            exit
         }
    
    # Get current state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName `
        -Status -DefaultProfile $AzureContext).Statuses[1].Code
    
    Write-Output "`r`n Beginning VM status: $status `r`n"
    
    # Start or stop VM based on current state
    if($status -eq "Powerstate/deallocated")
        {
            Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
        }
    elseif ($status -eq "Powerstate/running")
        {
            Stop-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
        }
    
    # Get new state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName -Status `
        -DefaultProfile $AzureContext).Statuses[1].Code  
    
    Write-Output "`r`n Ending VM status: $status `r`n `r`n"
    
    Write-Output "Account ID of current context: " $AzureContext.Account.Id
    ```

1. Dans l’éditeur, sur la ligne 8, modifiez la valeur de la variable `$automationAccount` si nécessaire.

1. Sélectionnez **Enregistrer**, puis **volet Test**.

1. Renseignez les paramètres `RESOURCEGROUP` et `VMNAME` avec les valeurs appropriées. Entrez `SA` pour le paramètre `METHOD`, et `xUAMI` pour le paramètre `UAMI`. Le runbook va tenter de modifier l’état d’alimentation de votre machine virtuelle à l’aide de l’identité managée affectée par le système.

1. Sélectionnez **Démarrer**. Une fois le runbook terminé, la sortie doit ressembler à ce qui suit :

    ```output
     Beginning VM status: PowerState/deallocated
    
    OperationId : 5b707401-f415-4268-9b43-be1f73ddc54b
    Status      : Succeeded
    StartTime   : 8/3/2021 10:52:09 PM
    EndTime     : 8/3/2021 10:52:50 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/running 
     
    Account ID of current context: 
    MSI@50342
    ```

1. Modifiez la valeur du paramètre `METHOD` en `UA`.

1. Sélectionnez **Démarrer**. Le runbook va tenter de modifier l’état d’alimentation de votre machine virtuelle à l’aide de l’identité managée affectée par l’utilisateur. Une fois le runbook terminé, la sortie doit ressembler à ce qui suit :

    ```output
    Using user-assigned managed identity
    
     Beginning VM status: PowerState/running 
    
    OperationId : 679fcadf-d0b9-406a-9282-66bc211a9fbf
    Status      : Succeeded
    StartTime   : 8/3/2021 11:06:03 PM
    EndTime     : 8/3/2021 11:06:49 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/deallocated 
     
    Account ID of current context: 
    9034f5d3-c46d-44d4-afd6-c78aeab837ea
    ```

## <a name="clean-up-resources"></a>Suppression des ressources

Pour supprimer toutes les ressources qui ne sont plus nécessaires, exécutez le runbook suivant.

```powershell
#Remove runbook
Remove-AzAutomationRunbook `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name "miTesting" `
    -Force

# Remove role assignments
Remove-AzRoleAssignment `
    -ObjectId $UAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role2

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un [runbook PowerShell](../automation-runbook-types.md#powershell-runbooks) dans Azure Automation qui utilise une [identité managée](../automation-security-overview.md#managed-identities), plutôt que le compte d’identification, pour interagir avec les ressources. Pour obtenir un aperçu des runbooks PowerShell Workflow, consultez :

> [!div class="nextstepaction"]
> [Tutoriel : Créer un runbook de workflow PowerShell](automation-tutorial-runbook-textual.md)