---
title: Supprimer une identité managée affectée par l’utilisateur pour un compte Azure Automation
description: Cet article explique comment supprimer une identité managée affectée par l’utilisateur pour un compte Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: 11db735a21e4d4007c35f1a82f1e5dfadb377fd1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432450"
---
# <a name="remove-user-assigned-managed-identity-for-azure-automation-account"></a>Supprimer une identité managée affectée par l’utilisateur pour un compte Azure Automation

Vous pouvez supprimer une identité managée affectée par l'utilisateur dans Azure Automation à l’aide du portail Azure, de PowerShell, de l’API REST Azure ou d’un modèle Azure Resource Manager (ARM).

## <a name="remove-using-the-azure-portal"></a>Supprimer à l’aide du portail Azure

Vous pouvez supprimer une identité managée affectée par l'utilisateur à partir du portail Azure quelle que soit la façon dont l’identité managée affectée par l’utilisateur a été initialement ajoutée.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Automation et sous **Paramètres du compte**, sélectionnez **Identité**.

1. Sélectionnez l’onglet **Affecté(e) par l’utilisateur**.

1. Sélectionnez l’identité managée affectée par l'utilisateur à supprimer de la liste.

1. Sélectionnez **Supprimer**. Lorsque vous êtes invité à confirmer, sélectionnez **Oui**.

L’identité managée affectée par l'utilisateur est supprimée et n’a plus accès à la ressource cible.

## <a name="remove-using-powershell"></a>Supprimer à l’aide de PowerShell

Utilisez la cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) PowerShell pour supprimer toutes les identités managées affectées par le système et conserver une identité managée affectée par le système existante.

1. Connectez-vous à Azure de manière interactive à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) et suivez les instructions.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. Fournissez une valeur appropriée pour les variables, puis exécutez le script.

    ```powershell
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    ```

1. Exécutez [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount).

    ```powershell
    # Removes all UAs, keeps SA
    $output = Set-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount `
        -AssignSystemIdentity 
    
    $output.identity.Type
    ```

    La sortie est la suivante `SystemAssigned`.

## <a name="remove-using-rest-api"></a>Supprimer à l’aide de l’API REST

Vous pouvez supprimer une identité managée affectée par l'utilisateur du compte Automation à l’aide de l’appel et l’exemple d’API REST suivants.

### <a name="request-body"></a>Corps de la demande

Scénario : l’identité managée affectée par le système est activée ou doit être activée. Une des nombreuses identités managées affectées par l’utilisateur doit être supprimée. Cet exemple supprime une identité managée affectée par l'utilisateur appelée `firstIdentity` à l’aide de la méthode **PATCH**.

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}
```

Scénario : l’identité managée affectée par le système est activée ou doit être activée. Toutes les identités managées affectées par l’utilisateur doivent être supprimées à l’aide de la méthode HTTP **PUT**.

```json
{
  "identity": {
    "type": "SystemAssigned"
  }
}
```

Scénario : l’identité managée affectée par le système est désactivée ou doit être désactivée. Une des nombreuses identités managées affectées par l’utilisateur doit être supprimée. Cet exemple supprime une identité managée affectée par l'utilisateur appelée `firstIdentity` à l’aide de la méthode **PATCH**.

```json
{
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}

```

Scénario : l’identité managée affectée par le système est désactivée ou doit être désactivée. Toutes les identités managées affectées par l’utilisateur doivent être supprimées à l’aide de la méthode HTTP **PUT**.

```json
{
  "identity": {
    "type": "None"
  }
}
```

Voici l’URI de la requête API REST du service pour envoyer la requête PATCH.

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>Exemple

Procédez comme suit.

1. Copiez et collez le corps de la requête, en fonction de l’opération que vous souhaitez effectuer, dans un fichier nommé `body_remove_ua.json`. Apportez les modifications nécessaires, puis enregistrez le fichier sur votre ordinateur local ou dans un compte de stockage Azure.

1. Connectez-vous à Azure de manière interactive à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) et suivez les instructions.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount -Subscription
    }
    ```

1. Fournissez une valeur appropriée pour les variables, puis exécutez le script.

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_ua.json"
    ```

1. Cet exemple utilise la cmdlet PowerShell [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) pour envoyer la requête PATCH à votre compte Automation.

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscriptionID/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount`?api-version=2020-01-13-preview"
    
    # build body
    $body = Get-Content $file
    
    # obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    
    # Invoke the REST API
    Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Confirm removal
    (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.Type
    ```

   Selon la syntaxe que vous avez utilisée, la sortie sera : `SystemAssignedUserAssigned`, `SystemAssigned`, `UserAssigned` ou vide.

## <a name="remove-using-azure-resource-manager-template"></a>Supprimer à l’aide d’un modèle Azure Resource Manager

Si vous avez ajouté l’identité managée affectée par l'utilisateur à votre compte Automation à l’aide d’un modèle Azure Resource Manager, vous pouvez supprimer l’identité managée affectée par l'utilisateur en modifiant le modèle, puis en le réexécutant.

Scénario : l’identité managée affectée par le système est activée ou doit être activée. Une des deux identités managées affectées par l’utilisateur doit être supprimée. Cet extrait de syntaxe supprime **toutes** les identités managées affectées par l’utilisateur **sauf** celle transmise en tant que paramètre au modèle.

```json
...
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

Scénario : l’identité managée affectée par le système est activée ou doit être activée. Toutes les identités managées affectées par l’utilisateur doivent être supprimées.

```json
...
"identity": {
    "type": "SystemAssigned"
},
...
```

Scénario : l’identité managée affectée par le système est désactivée ou doit être désactivée. Une des deux identités managées affectées par l’utilisateur doit être supprimée. Cet extrait de syntaxe supprime **toutes** les identités managées affectées par l’utilisateur **sauf** celle transmise en tant que paramètre au modèle.

```json
...
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

Utilisez la cmdlet [Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) pour vérifier. Selon la syntaxe que vous avez utilisée, la sortie sera : `SystemAssignedUserAssigned`,`SystemAssigned` ou `UserAssigned`.

```powershell
(Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity.Type
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’activation d’identités managées dans Azure Automation, consultez [Activer et utiliser l’identité managée pour Automation](enable-managed-identity-for-automation.md).

- Pour obtenir une vue d’ensemble de la sécurité du compte Automation, consultez [Vue d’ensemble de l’authentification du compte Automation](automation-security-overview.md).
