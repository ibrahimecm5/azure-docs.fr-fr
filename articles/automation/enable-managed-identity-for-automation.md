---
title: Utilisation d’une identité managée affectée par le système pour un compte Azure Automation
description: Cet article explique comment configurer une identité managée pour les comptes Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 685126603c302a02d56aff51873cd34340947494
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470753"
---
# <a name="using-a-system-assigned-managed-identity-for-an-azure-automation-account"></a>Utilisation d’une identité managée affectée par le système pour un compte Azure Automation

Cette rubrique vous montre comment activer une identité managée affectée par le système pour un compte Azure Automation et comment l’utiliser pour accéder à d’autres ressources. Pour plus d’informations sur le fonctionnement des identités managées avec Azure Automation, consultez [Identités managées](automation-security-overview.md#managed-identities).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Un compte Azure Automation. Pour obtenir des instructions, consultez [Créer un compte Azure Automation](./quickstarts/create-account-portal.md).

- La version la plus récente des modules Az PowerShell Az.Accounts, Az.Resources, Az.Automation et Az.KeyVault.

- Une ressource Azure à laquelle vous souhaitez accéder à partir de votre runbook Automation. Cette ressource doit avoir un rôle défini pour l’identité managée, ce qui aide le runbook Automation à authentifier l’accès à la ressource. Pour ajouter des rôles, vous devez être propriétaire de la ressource dans le locataire Azure AD correspondant.

- Si vous souhaitez exécuter des tâches hybrides à l’aide d’une identité managée, mettez à jour le runbook worker hybride vers la dernière version. Les versions minimales requises sont :

  - Runbook worker hybride Windows : version 7.3.1125.0
  - Runbook worker hybride Linux : version 1.7.4.0

## <a name="enable-a-system-assigned-managed-identity-for-an-azure-automation-account"></a>Activer une identité managée affectée par le système pour un compte Azure Automation

Une fois activées, les propriétés suivantes seront affectées à l’identité managée affectée par le système.

|Propriété (JSON) | Valeur | Description|
|----------|-----------|------------|
| principalid | \<principal-ID\> | L’Identificateur global unique (GUID) de l’objet du principal de service pour l’identité managée affectée par le système qui représente votre compte Automation dans le locataire Azure AD. Ce GUID apparaît parfois sous la forme d’un « ID d’objet » ou objectID. |
| tenantid | \<Azure-AD-tenant-ID\> | Identificateur global unique (GUID) qui représente le locataire Azure AD dont le compte Automation est maintenant membre. À l’intérieur du locataire Azure AD, le principal du service a le même nom que le compte Automation. |

Vous pouvez activer une identité managée affectée par le système pour un compte Azure Automation à l’aide du portail Azure, de PowerShell, de l’API REST Azure ou du modèle ARM. Pour les exemples impliquant PowerShell, connectez-vous d’abord à Azure de manière interactive à l’aide de l’applet de commande [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) et suivez les instructions.

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
```

> [!IMPORTANT]
> La nouvelle identité au niveau du compte Automation remplace toutes les identités attribuées par le système au niveau de la machine virtuelle précédentes, et décrites dans [Utiliser l’authentification du runbook avec les identités managées](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities). Si vous exécutez des tâches hybrides sur des machines virtuelles Azure qui utilisent l’identité affectée par le système d’une machine virtuelle pour accéder aux ressources du runbook, l’identité du compte Automation sera utilisée pour les tâches hybrides. Cela signifie que l’exécution de votre tâche existante peut être affectée si vous avez utilisé la fonctionnalité de clés gérées par le client (CMK) de votre compte Automation.<br/><br/>Si vous souhaitez continuer à utiliser l’identité managée de la machine virtuelle, vous ne devez pas activer l’identité au niveau du compte Automation. Si vous l’avez déjà activée, vous pouvez désactiver l’identité managée affectée par le système du compte Automation. Consultez [Désactiver votre identité managée de compte Azure Automation](./disable-managed-identity-for-automation.md).

### <a name="enable-using-the-azure-portal"></a>Activer à l’aide du portail Azure

Procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sur le Portail Azure, accédez à votre compte Automation.

1. Sous **Paramètres du compte**, sélectionnez **Identité**.

1. Affectez la valeur **On** (Activé) à l’option **Affecté(e) par le système**, puis appuyez sur **Enregistrer**. Lorsque vous êtes invité à confirmer, sélectionnez **Oui**.

   :::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Activation d’une identité affectée par le système dans le portail Azure.":::

   Votre compte Automation peut désormais utiliser l’identité affectée par le système qui est inscrite auprès d’Azure AD (Azure Active Directory) et représentée par un ID d’objet.

   :::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="ID d’objet de l’identité managée.":::

### <a name="enable-using-powershell"></a>Activer à l’aide de PowerShell

Utilisez l’applet de commande PowerShell [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) pour activer l’Identité managée affectée par le système.

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity

$output
```

Le résultat doit être semblable à ce qui suit :

:::image type="content" source="media/enable-managed-identity-for-automation/set-azautomationaccount-output.png" alt-text="Sortie de la commande Set-azautomationaccount.":::

Pour obtenir une sortie supplémentaire, modifiez l’exemple pour spécifier : `$output.identity | ConvertTo-Json`.

### <a name="enable-using-a-rest-api"></a>Activer à l’aide d’une API REST

Vous trouverez ci-dessous une syntaxe et des exemples d’étapes.

#### <a name="syntax"></a>Syntaxe

La syntaxe du corps ci-dessous active une identité managée affectée par le système à un compte Automation existant à l’aide de la méthode HTTP **PATCH**. Toutefois, cette syntaxe supprimera toutes les identités managées affectées par l’utilisateur existantes associées au compte Automation.

```json
{ 
 "identity": { 
   "type": "SystemAssigned" 
  } 
}
```

Si plusieurs identités affectées par l’utilisateur sont définies, pour les conserver et supprimer uniquement l’identité affectée par le système, vous devez spécifier chaque identité affectée par l’utilisateur à l’aide d’une liste délimitée par des virgules. L’exemple ci-dessous utilise la méthode HTTP **PATCH**.

```json
{ 
  "identity" : {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID2": {}
    }
  }
}

```

La syntaxe de l’API est la suivante :

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

#### <a name="example"></a>Exemple

Procédez comme suit.

1. Copiez et collez la syntaxe du corps dans un fichier nommé `body_sa.json`. Enregistrez le fichier sur votre ordinateur local ou dans un compte de stockage Azure.

1. Mettez à jour la valeur de la variable ci-dessous, puis exécutez.

    ```powershell
    $file = "path\body_sa.json"
    ```

1. Cet exemple utilise l’applet de commande PowerShell [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) pour envoyer la requête de PATCH à votre compte Automation.

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
        "PrincipalId":  "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "TenantId":  "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "Type":  0,
        "UserAssignedIdentities":  null
    }
    ```

### <a name="enable-using-an-arm-template"></a>Activer à l’aide d’un modèle ARM

Vous trouverez ci-dessous une syntaxe et des exemples d’étapes.

#### <a name="template-syntax"></a>Syntaxe des modèles

La syntaxe de l’exemple de modèle ci-dessous active une identité managée affectée par le système au compte Automation existant. Toutefois, cette syntaxe supprimera toutes les identités managées affectées par l’utilisateur existantes associées au compte Automation.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "yourAutomationAccount",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
        },
      "properties": {
        "sku": {
          "name": "Basic"
        }
      }
    }
  ]
}
```

#### <a name="example"></a>Exemple

Procédez comme suit.

1. Modifiez la syntaxe du modèle ci-dessus pour utiliser votre compte Automation et enregistrez-le dans un fichier nommé `template_sa.json`.

1. Mettez à jour la valeur de la variable ci-dessous, puis exécutez.

    ```powershell
    $templateFile = "path\template_sa.json"
    ```

1. Utilisez l’applet de commande PowerShell [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) pour déployer le modèle.

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "SystemAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile
    ```

   La commande ne génèrera pas de sortie. Toutefois, vous pouvez utiliser le code ci-dessous pour vérifier :

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

   La sortie ressemblera à la sortie affichée pour l’exemple d’API REST ci-dessus.

## <a name="give-access-to-azure-resources-by-obtaining-a-token"></a>Accorder à l’accès aux ressources Azure en obtenant un jeton

Un compte Automation peut utiliser son identité managée affectée par le système pour obtenir des jetons afin d’accéder à d’autres ressources protégées par Azure AD, comme Azure Key Vault. Ces jetons ne représentent pas un utilisateur spécifique de l’application. Ils représentent plutôt l’application qui accède à la ressource. Dans ce cas, par exemple, le jeton représente un compte Automation.

Avant de pouvoir vous servir de l’identité managée affectée par le système dans le cadre de l’authentification, configurez l’accès de cette identité à la ressource Azure où vous prévoyez de l’utiliser. Pour effectuer cette tâche, attribuez le rôle approprié à cette identité sur la ressource Azure cible.

Suivez le principe des privilèges minimum et attribuez avec précaution les seules autorisations nécessaires pour exécuter votre runbook. Par exemple, si le compte Automation est requis uniquement pour démarrer ou arrêter une machine virtuelle Azure, les autorisations affectées au compte d’identification ou à l’identité managée doivent servir uniquement à démarrer ou arrêter la machine virtuelle. De même, si un runbook lit à partir du stockage blob, attribuez des autorisations en lecture seule.

L’exemple suivant utilise Azure PowerShell pour montrer comment attribuer le rôle Contributeur dans l’abonnement à la ressource Azure cible. Le rôle Contributeur est utilisé à titre d’exemple et peut ne pas être requis dans votre cas.

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-system-assigned-managed-identity"></a>Authentifier l’accès à l’aide d’une identité managée affectée par le système

Après avoir activé l’identité managée pour votre compte Automation et accordé à une identité l’accès à la ressource cible, vous pouvez spécifier cette identité dans les runbooks pour les ressources qui prennent en charge l’identité managée. Pour la prise en charge des identités, utilisez l’applet de commande `Connect-AzAccount` de l’applet de commande Az. Consultez [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) dans les informations de référence sur PowerShell.

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
```

> [!NOTE]
> Si votre organisation utilise toujours les applets de commande AzureRM dépréciées, vous pouvez utiliser `Connect-AzureRMAccount -Identity`.

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Générer un jeton d’accès sans utiliser les applets de commande Azure

Pour les points de terminaison HTTP, vérifiez les points suivants.

- L’en-tête de métadonnées doit être présent et avoir la valeur « true ».
- Une ressource doit être passée avec la demande, en tant que paramètre de requête pour une demande GET et en tant que données de formulaire pour une demande POST.
- L’en-tête X-IDENTITY-HEADER doit être défini sur la valeur de la variable d’environnement IDENTITY_HEADER pour les runbooks workers hybrides.
- Le type de contenu de la demande Post doit être « application/x-www-form-urlencoded ».

### <a name="get-access-token-for-system-assigned-managed-identity-using-http-get"></a>Obtenir le jeton d’accès pour l’identité managée affectée par le système à l’aide de HTTP Get

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="get-access-token-for-system-assigned-identity-using-http-post"></a>Obtenir le jeton d’accès pour l’identité affectée par le système à l’aide de HTTP Post

```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

### <a name="using-system-assigned-managed-identity-in-azure-powershell"></a>Utilisation d’une identité managée affectée par le système dans Azure PowerShell

Pour plus d'informations, consultez [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret).

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="using-system-assigned-managed-identity-in-python-runbook"></a>Utilisation d’une identité managée affectée par le système dans Python Runbook

```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

### <a name="using-system-assigned-managed-identity-to-access-sql-database"></a>Utilisation d’une identité managée affectée par le système pour accéder à la Base de données SQL

Pour plus d'informations sur l'approvisionnement de l'accès à une base de données Azure SQL, consultez [Approvisionner un administrateur Azure AD (SQL Database)](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

## <a name="migrate-from-existing-run-as-accounts-to-managed-identity"></a>Migrer à partir de comptes d’identification existants vers une identité managée

Azure Automation fournit une authentification pour la gestion des ressources Azure Resource Manager ou des ressources déployées sur le modèle de déploiement classique avec un compte d’identification. Pour passer d’un compte d’identification à une identité managée pour l’authentification du runbook, suivez les étapes ci-dessous.

1. Activez une identité managée [affectée par le système](enable-managed-identity-for-automation.md), [affectée par l’utilisateur](add-user-assigned-identity.md), ou les deux types d’identités managées.
1. Accordez à l’identité managée les mêmes privilèges que les ressources Azure correspondant à ce que le compte d’identification a été affecté.
1. Mettez à jour vos runbooks pour l’authentification à l’aide de l’identité managée.
1. Modifiez des runbooks pour utiliser l’identité managée. Pour la prise en charge des identités, utilisez l’applet de commande `Connect-AzAccount` de l’applet de commande Az. Consultez [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) dans les informations de référence sur PowerShell.

   - Si vous utilisez des modules AzureRM, mettez à jour `AzureRM.Profile` vers la dernière version et remplacez à l’aide de l’applet de commande `Add-AzureRMAccount` avec `Connect-AzureRMAccount –Identity`.
   - Si vous utilisez des modules Az, effectuez une mise à jour vers la dernière version en suivant les étapes décrites dans l’article [Mettre à jour les modules Azure PowerShell](automation-update-azure-modules.md#update-az-modules).

## <a name="next-steps"></a>Étapes suivantes

- Si l’exécution de vos runbooks ne se termine pas correctement, consultez [Résoudre les problèmes d’identité managée Azure Automation](troubleshoot/managed-identity.md).

- Si vous devez désactiver une identité managée, consultez [Désactiver votre identité managée de compte Azure Automation](disable-managed-identity-for-automation.md).

- Pour obtenir une vue d’ensemble de la sécurité du compte Azure Automation, consultez [Vue d’ensemble de l’authentification du compte Automation](automation-security-overview.md).
