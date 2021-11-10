---
title: Utilisation d’une identité managée affectée par l’utilisateur pour un compte Azure Automation
description: Cet article explique comment configurer une identité managée affectée par l’utilisateur pour les comptes Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: 033f25f5d5902b339a2777cffc8c526a459ca587
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435526"
---
# <a name="using-a-user-assigned-managed-identity-for-an-azure-automation-account"></a>Utilisation d’une identité managée affectée par l’utilisateur pour un compte Azure Automation

Cette rubrique vous montre comment ajouter une identité managée affectée par l’utilisateur pour un compte Azure Automation et comment l’utiliser pour accéder à d’autres ressources. Pour plus d’informations sur le fonctionnement des identités managées avec Azure Automation, consultez [Identités managées](automation-security-overview.md#managed-identities).

> [!NOTE]
> Les identités managées affectées par l’utilisateur sont uniquement prises en charge pour les tâches cloud.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Un compte Azure Automation. Pour obtenir des instructions, consultez [Créer un compte Azure Automation](./quickstarts/create-account-portal.md).

- Une identité managée affectée par le système. Pour des instructions, consultez [À l’aide d’une identité managée affectée par le système pour votre compte Azure Automation](enable-managed-identity-for-automation.md).

- Identité managée affectée par l’utilisateur. Pour obtenir des instructions, consultez [Créer une identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

- L’identité managée affectée par l’utilisateur et les ressources Azure cibles que votre runbook gère à l’aide de cette identité doivent se trouver dans le même abonnement Azure.

- La dernière version des modules de compte Azure. Il s'agit actuellement de la version 2.2.8. (Pour plus d'informations sur cette version, consultez [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/)).

- Une ressource Azure à laquelle vous souhaitez accéder à partir de votre runbook Automation. Cette ressource doit avoir un rôle défini pour l’identité managée affectée par l’utilisateur, ce qui aide le runbook Automation à authentifier l’accès à la ressource. Pour ajouter des rôles, vous devez être propriétaire de la ressource dans le locataire Azure AD correspondant.

- Si vous souhaitez exécuter des tâches hybrides à l’aide d’une identité managée affectée par l’utilisateur, mettez à jour le Runbook Worker hybride vers la dernière version. Les versions minimales requises sont :

  - Runbook worker hybride Windows : version 7.3.1125.0
  - Runbook worker hybride Linux : version 1.7.4.0

## <a name="add-user-assigned-managed-identity-for-azure-automation-account"></a>Ajouter une identité managée affectée par l’utilisateur pour un compte Azure Automation

Vous pouvez ajouter une identité managée affectée par le système pour un compte Azure Automation à l’aide du Portail Azure, de PowerShell, de l’API REST Azure ou du modèle ARM. Pour les exemples impliquant PowerShell, connectez-vous d’abord à Azure de manière interactive à l’aide de l’applet de commande [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) et suivez les instructions.

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

Ensuite, initialisez un ensemble de variables qui seront utilisées dans les exemples. Modifiez les valeurs ci-dessous, puis exécutez.

```powershell
$subscriptionID = "subscriptionID"
$resourceGroup = "resourceGroupName"
$automationAccount = "automationAccountName"
$userAssignedOne = "userAssignedIdentityOne"
$userAssignedTwo = "userAssignedIdentityTwo"
```

### <a name="add-using-the-azure-portal"></a>Ajouter au moyen du Portail Azure

Procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sur le Portail Azure, accédez à votre compte Automation.

1. Sous **Paramètres du compte**, sélectionnez **Identité**.

1. Sélectionnez l’onglet **Affecté(e) par l’utilisateur**, puis **Ajouter**.

1. Sélectionnez votre identité managée affectée par l’utilisateur existante, puis sélectionnez **Ajouter**. Vous revenez à l’onglet **Affecté par l’utilisateur**.

   :::image type="content" source="media/add-user-assigned-identity/user-assigned-managed-identity.png" alt-text="Sortie à partir du portail.":::

### <a name="add-using-powershell"></a>Ajouter à l’aide de PowerShell

Utilisez la cmdlet PowerShell [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) pour ajouter les identités managées affectées par l’utilisateur. Vous devez d’abord déterminer s’il existe une identité managée affectée par le système existante. L’exemple ci-dessous ajoute deux identités managées affectées par l’utilisateur existantes à un compte Automation existant, et désactive une identité managée affectée par le système, le cas échéant.

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo"

$output
```

Pour conserver une identité managée affectée par le système existante, utilisez ce qui suit :

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo" `
    -AssignSystemIdentity

$output
```

Le résultat doit être semblable à ce qui suit :

:::image type="content" source="media/add-user-assigned-identity/set-azautomationaccount-output.png" alt-text="Sortie de la commande Set-AzAutomationAccount.":::

Pour obtenir une sortie supplémentaire, exécutez : `$output.identity | ConvertTo-Json`.

### <a name="add-using-a-rest-api"></a>Ajouter à l’aide d’une API REST

Vous trouverez ci-dessous la syntaxe et des exemples d’étapes.

#### <a name="syntax"></a>Syntaxe

L’exemple de syntaxe de corps ci-dessous active une identité managée affectée par le système si elle n’est pas déjà activée et affecte deux identités managées affectées par l’utilisateur existantes au compte Automation existant.

PATCH

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
    }
  }
}
```

La syntaxe de l’API est la suivante :

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview 
```

#### <a name="example"></a>Exemple

Procédez comme suit.

1. Révisez la syntaxe du corps ci-dessus dans un fichier nommé `body_ua.json`. Enregistrez le fichier sur votre ordinateur local ou dans un compte de stockage Azure.

1. Révisez la valeur de la variable ci-dessous, puis exécutez.

    ```powershell
    $file = "path\body_ua.json"
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
    $response = Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Review output
    $response.identity | ConvertTo-Json
    ```

    Le résultat doit être semblable à ce qui suit :

    ```json
    {
    "type": "SystemAssigned, UserAssigned",
    "principalId": "00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "userAssignedIdentities":  {
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI1":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    },
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI2":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    }
        }
    }
    ```

### <a name="add-using-an-arm-template"></a>Ajouter à l’aide d’un modèle ARM

Vous trouverez ci-dessous la syntaxe et des exemples d’étapes.

#### <a name="template-syntax"></a>Syntaxe des modèles

L’exemple de syntaxe de modèle ci-dessous active une identité managée affectée par le système si elle n’est pas déjà activée et affecte deux identités managées affectées par l’utilisateur existantes au compte Automation existant.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccountName": {
     "defaultValue": "YourAutomationAccount",
      "type": "String",
      "metadata": {
        "description": "Automation account name"
      }
    },
    "userAssignedOne": {
     "defaultValue": "userAssignedOne",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      },
    "userAssignedTwo": {
     "defaultValue": "userAssignedTwo",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      }
   },
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "[parameters('automationAccountName')]",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned, UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {},
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedTwo'))]": {}
        }
      },
      "properties": {
        "sku": {
          "name": "Basic"
        },
        "encryption": {
          "keySource": "Microsoft.Automation",
          "identity": {}
        }
      }
    }
  ]
}
```

#### <a name="example"></a>Exemple

Procédez comme suit.

1. Copiez et collez le modèle dans un fichier nommé `template_ua.json`. Enregistrez le fichier sur votre ordinateur local ou dans un compte de stockage Azure.

1. Révisez la valeur de la variable ci-dessous, puis exécutez.

    ```powershell
    $templateFile = "path\template_ua.json"
    ```

1. Utilisez la cmdlet PowerShell [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) pour déployer le modèle.

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "UserAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $automationAccount `
        -userAssignedOne $userAssignedOne `
        -userAssignedTwo $userAssignedTwo
    ```

   La commande ne génèrera pas de sortie. Toutefois, vous pouvez utiliser le code ci-dessous pour vérifier :

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

    La sortie ressemblera à la sortie affichée pour l’exemple d’API REST ci-dessus.

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Accorder à l’identité un accès aux ressources Azure en obtenant un jeton

Un compte Automation peut utiliser son identité managée affectée par l’utilisateur pour obtenir des jetons afin d’accéder à d’autres ressources protégées par Azure AD, comme Azure Key Vault. Ces jetons ne représentent pas un utilisateur spécifique de l’application. Ils représentent plutôt l’application qui accède à la ressource. Dans ce cas, par exemple, le jeton représente un compte Automation.

Avant de pouvoir vous servir de l’identité managée affectée par l’utilisateur dans le cadre de l’authentification, configurez l’accès de cette identité à la ressource Azure où vous prévoyez de l’utiliser. Pour effectuer cette tâche, attribuez le rôle approprié à cette identité sur la ressource Azure cible.

Suivez le principe des privilèges minimum et attribuez avec précaution les seules autorisations nécessaires pour exécuter votre runbook. Par exemple, si le compte Automation est requis uniquement pour démarrer ou arrêter une machine virtuelle Azure, les autorisations affectées au compte d’identification ou à l’identité managée doivent servir uniquement à démarrer ou arrêter la machine virtuelle. De même, si un runbook lit à partir du stockage blob, attribuez des autorisations en lecture seule.

Cet exemple utilise Azure PowerShell pour montrer comment attribuer le rôle Contributeur dans l’abonnement à la ressource Azure cible. Le rôle Contributeur est utilisé à titre d’exemple et peut ne pas être requis dans votre cas. Vous pouvez également attribuer le rôle à la ressource Azure cible dans le [portail Azure](../role-based-access-control/role-assignments-portal.md).

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-user-assigned-managed-identity"></a>Authentifier l’accès à l’aide d’une identité managée affectée par l’utilisateur

Après avoir activé l’identité managée affectée par l’utilisateur pour votre compte Automation et accordé à une identité l’accès à la ressource cible, vous pouvez spécifier cette identité dans les runbooks pour les ressources qui prennent en charge l’identité managée. Pour la prise en charge des identités, utilisez la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount).

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity -AccountId <user-assigned-identity-ClientId>).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
```

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Générer un jeton d’accès sans utiliser les applets de commande Azure

Pour les points de terminaison HTTP, vérifiez les points suivants.
- L’en-tête de métadonnées doit être présent et avoir la valeur « true ».
- Une ressource doit être passée avec la demande, en tant que paramètre de requête pour une demande GET et en tant que données de formulaire pour une demande POST.
- Le type de contenu de la demande de publication doit être `application/x-www-form-urlencoded`.

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-get"></a>Obtenir le jeton d’accès pour l’identité managée affectée par l’utilisateur à l’aide de la requête HTTP Get  

```powershell
$resource= "?resource=https://management.azure.com/"
$client_id="&client_id=<ClientId of USI>"
$url = $env:IDENTITY_ENDPOINT + $resource + $client_id 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"  
$Headers.Add("Metadata", "True")
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token 
```

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-post"></a>Obtenir le jeton d’accès pour l’identité managée affectée par l’utilisateur à l’aide de la requête HTTP Post

```powershell
$url = $env:IDENTITY_ENDPOINT
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Metadata", "True")
$body = @{'resource'='https://management.azure.com/' 
'client_id'='<ClientId of USI>'}
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body
Write-Output $accessToken.access_token 
```

### <a name="using-user-assigned-managed-identity-in-azure-powershell"></a>Utilisation d’une identité managée affectée par l’utilisateur dans Azure PowerShell

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity -AccountId <ClientId of USI>"  
Connect-AzAccount -Identity -AccountId <ClientId of USI> 
Write-Output "Successfully connected with Automation account's Managed Identity"  
Write-Output "Trying to fetch value from key vault using User Assigned Managed identity. Make sure you have given correct access to Managed Identity"  
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>'  
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)  
try {  
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)  
    Write-Output $secretValueText  
} finally {  
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)  
} 
```

### <a name="using-user-assigned-managed-identity-in-python-runbook"></a>Utilisation d’une identité managée affectée par l’utilisateur dans Python Runbook

```python
#!/usr/bin/env python3  
import os  
import requests   

resource = "?resource=https://management.azure.com/" 
client_id = "&client_id=<ClientId of USI>" 
endPoint = os.getenv('IDENTITY_ENDPOINT')+ resource +client_id 
payload={}  
headers = {  
  'Metadata': 'True'  
}  
response = requests.request("GET", endPoint, headers=headers, data=payload)  
print(response.text) 
```

## <a name="next-steps"></a>Étapes suivantes

- Si l’exécution de vos runbooks ne se termine pas correctement, consultez [Résoudre les problèmes d’identité managée Azure Automation](troubleshoot/managed-identity.md).

- Si vous devez désactiver une identité managée, consultez [Désactiver votre identité managée de compte Azure Automation](disable-managed-identity-for-automation.md).

- Pour obtenir une vue d’ensemble de la sécurité du compte Azure Automation, consultez [Vue d’ensemble de l’authentification du compte Automation](automation-security-overview.md).