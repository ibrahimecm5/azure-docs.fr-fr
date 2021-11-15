---
title: 'Démarrage rapide : Créer un laboratoire avec une API REST'
description: Dans ce démarrage rapide, vous créez un laboratoire dans Azure DevTest Labs à l’aide d’une API REST Azure.
ms.topic: quickstart
ms.date: 10/27/2021
ms.openlocfilehash: 83a1509f36f53d51f63f7dbc39ea2d1f6794dc54
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479178"
---
# <a name="quickstart-create-a-lab-in-azure-devtest-labs-using-azure-rest-api"></a>Démarrage rapide : Créer un laboratoire dans Azure DevTest Labs à l’aide d’une API REST Azure

Prenez en main Azure DevTest Labs à l’aide de l’API REST Azure. Azure DevTest Labs inclut un groupe de ressources, comme des réseaux et des machines virtuelles Azure. Cette infrastructure vous permet de mieux gérer ces ressources en spécifiant des limites et des quotas.  L’API REST Azure vous permet de gérer des opérations sur les services hébergés dans la plateforme Azure.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Le [module Az](/powershell/azure/new-azureps-module-az) PowerShell installé. Vérifiez que vous avez la version la plus récente. Si nécessaire, exécutez `Update-Module -Name Az`.

## <a name="prepare-request-body"></a>Préparer le corps de la demande

Préparez le [corps de la demande](/rest/api/dtl/labs/create-or-update.md#request-body) qui sera consommé par l’appel REST.

Copiez et collez la syntaxe JSON suivante dans un fichier nommé `body.json`. Enregistrez le fichier sur votre ordinateur local ou dans un compte de stockage Azure.

```json
{
  "properties": {
    "labStorageType": "Standard"
  },
  "location": "westus2",
  "tags": {
    "Env": "alpha"
  }
}
```

## <a name="sign-in-to-your-azure-subscription"></a>Connectez-vous à votre abonnement Azure :

1. Fournissez une valeur appropriée pour les variables, puis exécutez le script.

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    $file = "path\body.json"
    ```

1. Depuis votre station de travail, connectez-vous à votre abonnement Azure avec la cmdlet PowerShell [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) et suivez les instructions qui s’affichent à l’écran.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId $subscription
    ```

## <a name="build-request-body-for-submission"></a>Créer le corps de la demande pour l’envoyer

La syntaxe de la demande PUT est la suivante :<br>
 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{name}?api-version=2018-09-15`.

 Exécutez les scripts PowerShell suivants pour transmettre la valeur de la demande à un paramètre. Le contenu du corps de la demande est également transmis à un paramètre.

```powershell
# build URI
$URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"

# build body
$body = Get-Content $file
```

## <a name="obtain-an-authentication-token"></a>Obtenir un jeton d’authentification

Utilisez les commandes suivantes pour récupérer un jeton d’authentification :

```powershell
$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}
```

## <a name="invoke-the-rest-api"></a>Appeler l’API REST

Utilisez les commandes suivantes pour appeler l’API REST et examinez la réponse.

```powershell
# Invoke the REST API
$response = Invoke-RestMethod -Uri $URI -Method PUT -Headers $authHeader -Body $body

# Review output
$response | ConvertTo-Json
```

La réponse doit être semblable au texte suivant :

```output
{
    "properties":  {
                       "labStorageType":  "Standard",
                       "mandatoryArtifactsResourceIdsLinux":  [

                                                              ],
                       "mandatoryArtifactsResourceIdsWindows":  [

                                                                ],
                       "createdDate":  "2021-10-27T20:22:49.7495913+00:00",
                       "premiumDataDisks":  "Disabled",
                       "environmentPermission":  "Reader",
                       "announcement":  {
                                            "title":  "",
                                            "markdown":  "",
                                            "enabled":  "Disabled",
                                            "expired":  false
                                        },
                       "support":  {
                                       "enabled":  "Disabled",
                                       "markdown":  ""
                                   },
                       "provisioningState":  "Creating",
                       "uniqueIdentifier":  "uniqueID"
                   },
    "id":  "/subscriptions/ContosoID/resourcegroups/groupcontoso/providers/microsoft.devtestlab/labs/myotherlab",

    "name":  "myOtherLab",
    "type":  "Microsoft.DevTestLab/labs",
    "location":  "westus2",
    "tags":  {
                 "Env":  "alpha"
             }
}
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser ce laboratoire, supprimez-le en procédant comme suit :

1. Fournissez une valeur appropriée pour les variables, puis exécutez le script.

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    ```

1. Exécutez le script suivant pour supprimer le laboratoire nommé d’Azure DevTest Labs.

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"
    
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
    Invoke-RestMethod -Uri $URI -Method DELETE -Headers $authHeader
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un laboratoire à l’aide de l’API REST Azure. Pour savoir comment accéder au laboratoire, passer au tutoriel suivant :

> [!div class="nextstepaction"]
> [Didacticiel : Accéder au laboratoire](../tutorial-use-custom-lab.md)
