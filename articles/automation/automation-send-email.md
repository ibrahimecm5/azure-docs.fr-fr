---
title: Envoyer un e-mail à partir d’un runbook Azure Automation
description: Cet article explique comment envoyer un e-mail à partir d’un runbook.
services: automation
ms.subservice: process-automation
ms.date: 09/21/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c5f89a755cecd2dcf36d3a8a41711fe941108ffa
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427263"
---
# <a name="send-an-email-from-am-automation-runbook"></a>Envoyer un e-mail à partir d’un runbook Automation

Vous pouvez envoyer un e-mail à partir d’un runbook avec [SendGrid](https://sendgrid.com/solutions) à l’aide de PowerShell. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* [Un compte SendGrid](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account)
* Vérification de l’expéditeur SendGrid. [Domain (Domaine) ou Single Sender (Expéditeur unique)](https://sendgrid.com/docs/for-developers/sending-email/sender-identity/) 
* Votre [clé d’API SendGrid](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key).

* Un compte Azure Automation avec au moins une identité managée affectée par l’utilisateur. Pour plus d’informations, consultez [Activer les identités managées](./quickstarts/enable-managed-identity.md).
* Modules Az : `Az.Accounts` et `Az.KeyVault` importés dans le compte Automation. Pour plus d’informations, consultez [Importer des modules Az](./shared-resources/modules.md#import-az-modules).
* Le [module PowerShell Azure Az](/powershell/azure/new-azureps-module-az) installé sur votre ordinateur. Pour plus d'informations sur son installation ou sa mise à niveau, consultez [Installer le module PowerShell Azure Az](/powershell/azure/install-az-ps).

## <a name="create-an-azure-key-vault"></a>Créer un Azure Key Vault

Créez une instance Azure Key Vault et [stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md) qui permet aux informations d’identification d’accéder et de définir les secrets de coffre de clés dans le coffre de clés spécifié.

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
    $resourceGroup = "<Resource group>"
    $automationAccount = "<Automation account>"
    $region = "<Region>"
    $SendGridAPIKey = "<SendGrid API key>"
    $VaultName = "<A universally unique vault name>"

    $userAssignedManagedIdentity = "<User-assigned managed identity>"
    ```

1. Créer une instance Key Vault et assigner des autorisations

    ```powershell
    # Create the new key vault
    $newKeyVault = New-AzKeyVault `
        -VaultName $VaultName `
        -ResourceGroupName $resourceGroup `
        -Location $region

    $resourceId = $newKeyVault.ResourceId
    
    # Convert the SendGrid API key into a SecureString
    $Secret = ConvertTo-SecureString -String $SendGridAPIKey `
        -AsPlainText -Force

    Set-AzKeyVaultSecret -VaultName $VaultName `
        -Name 'SendGridAPIKey' `
        -SecretValue $Secret
    
    # Grant Key Vault access to the Automation account's system-assigned managed identity.
    $SA_PrincipalId = (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.PrincipalId

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $SA_PrincipalId `
        -PermissionsToSecrets Set, Get

    # Grant Key Vault access to the user-assigned managed identity.
    $UAMI = Get-AzUserAssignedIdentity `
        -ResourceGroupName $resourceGroup `
        -Name $userAssignedManagedIdentity

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $UAMI.PrincipalId `
        -PermissionsToSecrets Set, Get
    ```

   Pour découvrir d’autres méthodes de création d’un coffre de clés Azure et de stockage d’un secret, consultez [Démarrages rapides Key Vault](../key-vault/index.yml).

## <a name="assign-permissions-to-managed-identities"></a>Attribuer des autorisations aux identités managées

Affectez des autorisations à l’[identité managée](./automation-security-overview.md#managed-identities) appropriée. Le runbook peut utiliser l’identité managée affectée par le système du compte Automation ou une identité managée affectée par l’utilisateur. Des étapes sont fournies pour affecter des autorisations à chaque identité. Les étapes ci-dessous utilisent PowerShell. Si vous préférez utiliser le portail, consultez [Attribuer des rôles Azure à l’aide du portail Azure](./../role-based-access-control/role-assignments-portal.md).

1. Utilisez la cmdlet PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) pour affecter un rôle à l’identité managée affectée par le système.

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $SA_PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. Attribuer un rôle à une identité managée affectée par l'utilisateur.

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId`
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. Pour l’identité managée affectée par le système, affichez `ClientId` et enregistrez la valeur pour une utilisation ultérieure.

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-the-runbook-to-send-an-email"></a>Créer le runbook pour envoyer un e-mail

Une fois que vous avez créé un coffre de clés et stocké votre clé API `SendGrid`, il est temps de créer le runbook qui récupère la clé API et envoie un e-mail. Utilisons un runbook qui utilise l'[Identité managée affectée par le système](./automation-security-overview.md#managed-identities) pour s'authentifier auprès d'Azure et extraire le secret d'Azure Key Vault. Nous allons appeler le runbook **Send-GridMailMessage**. Vous pouvez modifier le script PowerShell utilisé pour différents scénarios.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre compte Automation.

1. Dans la page de votre compte Automation ouvert, sous **Automatisation de processus**, sélectionnez **Runbooks**

1. Sélectionnez **+ Créer un runbook**.
    1. Nommez le runbook `Send-GridMailMessage`.
    1. Dans la liste déroulante **Type de runbook**, sélectionnez **PowerShell**.
    1. Sélectionnez **Create** (Créer).

   ![Créer un Runbook](./media/automation-send-email/automation-send-email-runbook.png)

1. Le runbook est créé et la page Modifier un runbook PowerShell s’ouvre.
   ![Modifier le runbook](./media/automation-send-email/automation-send-email-edit.png)

1. Copiez l’exemple PowerShell suivant dans la page Modifier. Vérifiez que le `VaultName` spécifie le nom que vous avez choisi pour votre coffre de clés.

    ```powershell
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content,
      [Parameter(Mandatory=$True)]
      [String] $ResourceGroupName
    )

    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context

    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext 

    $VaultName = "<Enter your vault name>"

    $SENDGRID_API_KEY = Get-AzKeyVaultSecret `
        -VaultName $VaultName `
        -Name "SendGridAPIKey" `
        -AsPlainText -DefaultProfile $AzureContext

    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

1. Si vous souhaitez que le runbook s’exécute avec l’identité managée affectée par le système, laissez le code tel quel. Si vous préférez utiliser une identité managée affectée par l’utilisateur, procédez comme suit :
    1. À la ligne 18, supprimez `$AzureContext = (Connect-AzAccount -Identity).context`,
    1. Remplacez-la par `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` et
    1. Entrez l'ID client que vous avez obtenu précédemment.

1. Sélectionnez **Enregistrer**, **Publier**, puis **Oui** lorsque vous y êtes invité.

Pour vérifier que le runbook s’exécute correctement, vous pouvez suivre les étapes décrites dans [Tester un runbook](manage-runbooks.md#test-a-runbook) ou [Démarrer un runbook](start-runbooks.md).

Si vous ne voyez pas votre e-mail de test, vérifiez vos dossiers de **courrier indésirable** et de **spam**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

1. Quand le runbook n’est plus nécessaire, sélectionnez-le dans la liste de runbooks et sélectionnez **Supprimer**.

1. Supprimez le coffre de clés à l’aide de l’applet de commande [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault).

    ```powershell
    $VaultName = "<your KeyVault name>"
    $resourceGroup = "<your ResourceGroup name>"
    Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $resourceGroup
    ```

## <a name="next-steps"></a>Étapes suivantes

* Pour envoyer des données de travaux de runbook à votre espace de travail Log Analytics, consultez [Transférer des données de travaux Azure Automation aux journaux Azure Monitor](automation-manage-send-joblogs-log-analytics.md).
* Pour superviser les journaux et les métriques de base, consultez [Utiliser une alerte pour déclencher un runbook Azure Automation](automation-create-alert-triggered-runbook.md).
