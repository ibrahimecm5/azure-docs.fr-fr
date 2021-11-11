---
title: Désactiver une identité managée affectée par le système pour un compte Azure Automation
description: Cet article explique comment désactiver une identité managée affectée par le système pour un compte Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: b3bf648914635e6ea9345a234c49bfb64c757bc1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435412"
---
# <a name="disable-system-assigned-managed-identity-for-azure-automation-account"></a>Désactiver une identité managée affectée par le système pour un compte Azure Automation

Vous pouvez désactiver une identité managée affectée par le système dans Azure Automation à l’aide du portail Azure ou de l’API REST.

## <a name="disable-using-the-azure-portal"></a>Désactiver à l’aide du portail Azure

Vous pouvez désactiver l’identité managée affectée par le système à partir du portail Azure quelle que soit la façon dont l’identité managée affectée par le système a été initialement configurée.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Automation et sous **Paramètres du compte**, sélectionnez **Identité**.

1. Dans l’onglet **Affectée par le système**, sous le bouton **État**, sélectionnez **Désactivé**, puis **Enregistrer**. Lorsque vous êtes invité à confirmer, sélectionnez **Oui**.

L’identité managée affectée par le système est désactivée et n’a plus accès à la ressource cible.

## <a name="disable-using-rest-api"></a>Désactiver à l’aide de l’API REST

Vous trouverez ci-dessous la syntaxe et des exemples d’étapes.

### <a name="request-body"></a>Corps de la demande

Le corps de la requête suivante désactive l’identité managée affectée par le système et supprime toutes les identités managées affectées par l’utilisateur à l’aide de la méthode **PATCH** HTTP.

```json
{ 
 "identity": { 
   "type": "None" 
  } 
}

```

Si plusieurs identités affectées par l’utilisateur sont définies, pour les conserver et supprimer uniquement l’identité affectée par le système, vous devez spécifier chaque identité affectée par l’utilisateur à l’aide d’une liste délimitée par des virgules. L’exemple ci-dessous utilise la méthode **PATCH** HTTP.

```json
{ 
"identity" : {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
        }
    }
}
```

Voici l’URI de la requête API REST du service pour envoyer la requête PATCH.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>Exemple

Procédez comme suit.

1. Copiez et collez le corps de la requête, en fonction de l’opération que vous souhaitez effectuer, dans un fichier nommé `body_remove_sa.json`. Enregistrez le fichier sur votre ordinateur local ou dans un compte de stockage Azure.

1. Connectez-vous à Azure de manière interactive à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) et suivez les instructions.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. Fournissez une valeur appropriée pour les variables, puis exécutez le script.

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_sa.json"
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

    Selon la syntaxe que vous avez utilisée, la sortie sera : `UserAssigned` ou vide.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’activation d’identités managées dans Azure Automation, consultez [Activer et utiliser l’identité managée pour Automation](enable-managed-identity-for-automation.md).

- Pour obtenir une vue d’ensemble de la sécurité du compte Automation, consultez [Vue d’ensemble de l’authentification du compte Automation](automation-security-overview.md).