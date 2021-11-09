---
title: Démarrer un runbook Azure Automation depuis un Webhook
description: Cet article vous explique comment utiliser un Webhook pour démarrer un runbook dans Azure Automation à partir d’un appel HTTP.
services: automation
ms.subservice: process-automation
ms.date: 07/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e79feb7b45500a53bc7e13557aedcd1c0410554b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026446"
---
# <a name="start-a-runbook-from-a-webhook"></a>Démarrer un runbook depuis un Webhook

Un webhook permet à un service externe de démarrer un runbook particulier dans Azure Automation via une simple requête HTTP. Ces services externes incluent Azure DevOps Services, GitHub, les journaux Azure Monitor et les applications personnalisées. De tels services peuvent utiliser un Webhook pour démarrer un runbook sans implémenter toute l’API Azure Automation. Pour comparer les webhooks aux autres méthodes de démarrage d’un runbook, consultez [Démarrage d’un runbook dans Azure Automation](./start-runbooks.md).

> [!NOTE]
> L’utilisation d’un webhook pour démarrer un runbook Python n’est pas prise en charge.

![Présentation des webhooks](media/automation-webhooks/webhook-overview-image.png)

Pour comprendre la configuration requise du client pour le protocole TLS 1.2 avec les webhooks, consultez [TLS 1.2 pour Azure Automation](automation-managing-data.md#tls-12-for-azure-automation).

## <a name="webhook-properties"></a>Propriétés des webhooks

Le tableau suivant décrit les propriétés que vous devez configurer pour un webhook.

| Propriété | Description |
|:--- |:--- |
| Nom |Nom du webhook. Vous pouvez attribuer le nom de votre choix, puisque celui-ci n’est pas visible par le client. Il sert seulement à identifier le runbook dans Azure Automation. À titre de meilleure pratique, nommez le webhook d’après le client qui l’utilise. |
| URL |URL du webhook. Il s’agit de l’adresse unique qu’un client appelle avec une méthode HTTP POST pour démarrer le runbook lié au webhook. Elle est générée automatiquement lorsque vous créez le webhook. Vous ne pouvez pas spécifier d’URL personnalisée. <br> <br> L’URL contient un jeton de sécurité qui permet à un système tiers d’appeler le runbook sans authentification supplémentaire. Pour cette raison, vous devez traiter l’URL comme un mot de passe. Pour des raisons de sécurité, vous pouvez uniquement afficher l’URL dans le portail Azure au moment de la création du webhook. Notez l'URL dans un emplacement sécurisé en vue d'une utilisation ultérieure. |
| Date d'expiration | Date d’expiration du webhook, après laquelle il ne peut plus être utilisé. Vous pouvez modifier la date d’expiration du webhook après sa création, tant que celui-ci n’a pas expiré. |
| activé | Paramètre indiquant si le webhook doit être activé par défaut lors de sa création. Si vous affectez la valeur Disabled à cette propriété, aucun client ne pourra utiliser le webhook. Vous pouvez définir cette propriété pendant ou après la création du webhook. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Paramètres utilisés lorsque le webhook démarre un runbook

Un webhook peut définir les valeurs des paramètres du runbook qui sont utilisées au démarrage de celui-ci. Le webhook doit inclure les valeurs de tous les paramètres obligatoires du runbook, et peut inclure les valeurs des paramètres facultatifs. Une valeur de paramètre configurée pour un webhook peut être modifiée même après la création de celui-ci. Plusieurs webhooks liés à un même runbook peuvent utiliser des valeurs différentes pour les paramètres du runbook. Lorsqu’un client démarre un runbook à l’aide d’un webhook, il ne peut pas remplacer les valeurs de paramètres définies dans le webhook.

Pour recevoir des données du client, le runbook accepte un paramètre unique appelé `WebhookData`. Ce paramètre définit un objet contenant les données que le client inclut dans une requête POST.

![Propriétés WebhookData](media/automation-webhooks/webhook-data-properties.png)

Le paramètre `WebhookData` possède les propriétés suivantes :

| Propriété | Description |
|:--- |:--- |
| WebhookName | Nom du webhook. |
| RequestHeader | Table de hachage contenant les en-têtes de la requête POST entrante. |
| RequestBody | Corps de la requête POST entrante. Le corps conserve la mise en forme des données (chaînes, JSON, XML ou données encodées dans un formulaire). Le Runbook doit être écrit pour fonctionner avec le format de données qui est attendu. |

Aucune configuration du webhook n’est nécessaire pour prendre en charge le paramètre `WebhookData`. En outre, le runbook n’est pas obligé de l’accepter. Si le runbook ne définit pas le paramètre, tous les détails de la requête envoyée à partir du client sont ignorés.

> [!NOTE]
> Lors de l’appel d’un webhook, le client doit toujours stocker toutes les valeurs de paramètre en cas d’échec de l’appel. En cas de panne réseau ou de problème de connexion, l’application ne pourra pas récupérer les appels de webhook qui ont échoué.

Si vous spécifiez une valeur pour `WebhookData` lors de la création du webhook, celle-ci est remplacée quand le webhook démarre le runbook avec les données de la demande POST du client. Cela se produit même si l’application n’inclut aucune donnée dans le corps de la demande.

Si vous démarrez un runbook qui définit `WebhookData` à l’aide d’un mécanisme autre qu’un webhook, vous pouvez fournir une valeur pour `WebhookData` que le runbook reconnaîtra. Cette valeur doit correspondre à un objet ayant les mêmes [propriétés](#webhook-properties) que le paramètre `WebhookData`, afin que le runbook puisse l’utiliser de la même manière qu’il utilise les objets `WebhookData` qui sont passés par un webhook.

Par exemple, si vous démarrez le runbook suivant à partir du portail Azure et souhaitez passer des exemples de données de webhook à des fins de test, vous devez passer ces données au format JSON dans l’interface utilisateur.

![Paramètre WebhookData à partir de l'interface utilisateur](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Dans l’exemple de runbook suivant, nous allons définir les propriétés suivantes pour `WebhookData` :

* **WebhookName** : MyWebhook
* **RequestBody** : `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Nous passons maintenant l’objet JSON suivant dans l’interface utilisateur pour le paramètre `WebhookData`. Cet exemple, avec les retours chariot et les caractères de nouvelle ligne, correspond au format qui est passé à partir d’un webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Démarrage du paramètre WebhookData à partir de l'interface utilisateur](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation journalise les valeurs de tous les paramètres d’entrée associés au travail de runbook. Par conséquent, toutes les entrées fournies par le client dans la requête webhook seront journalisées et accessibles à toute personne ayant accès au travail Automation. Pour cette raison, soyez prudent lorsque vous incluez des informations sensibles dans les appels du webhook.

## <a name="webhook-security"></a>Sécurité des webhooks

La sécurité d’un webhook dépend de la confidentialité de son URL, laquelle contient un jeton de sécurité permettant au webhook d’être appelé. Azure Automation n’effectue pas d’authentification de la demande tant que celle-ci est adressée à l’URL appropriée. Par conséquent, vos clients ne doivent pas utiliser les webhooks pour les runbooks qui exécutent des opérations hautement sensibles, sans recourir à un autre moyen de validation de la demande.

Tenez compte des stratégies suivantes :

* Vous pouvez inclure une logique dans un runbook pour déterminer si celui-ci est appelé par un webhook. Indiquez au runbook de vérifier la propriété `WebhookName` du paramètre `WebhookData`. Le runbook peut effectuer une validation supplémentaire en recherchant des informations spécifiques dans les propriétés `RequestHeader` et `RequestBody`.

* Faites en sorte que le runbook valide une condition externe quand il reçoit une demande de webhook. Par exemple, imaginez un runbook qui soit appelé par GitHub à chaque nouvelle validation effectuée dans un dépôt GitHub. Avant de poursuivre, le runbook peut se connecter à GitHub pour vérifier qu’une nouvelle validation s’est produite.

* Azure Automation prend en charge les étiquettes de service de réseau virtuel Azure, plus précisément [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Vous pouvez utiliser des étiquettes de service pour définir des contrôles d’accès réseau sur des [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md#security-rules) ou sur le [pare-feu Azure](../firewall/service-tags.md) et déclencher des webhooks à partir de votre réseau virtuel. Les étiquettes de service peuvent être utilisées à la place d’adresses IP spécifiques pendant la création de règles de sécurité. En spécifiant le nom d’étiquette de service **GuestAndHybridManagement** dans le champ source ou de destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service Automation. Cette étiquette de service ne permet pas d’autoriser un contrôle plus précis en limitant les plages d’adresses IP à une région spécifique.

## <a name="create-a-webhook"></a>Créer un webhook

Un webhook a besoin d’un runbook publié. Cette procédure pas à pas utilise une version modifiée du runbook créé dans [Créer un runbook Azure Automation](./learn/powershell-runbook-managed-identity.md). Pour suivre la procédure, modifiez votre runbook PowerShell avec le code suivant :

```powershell
param
(
    [Parameter(Mandatory=$false)]
    [object] $WebhookData
)

if ($WebhookData.RequestBody) { 
    $names = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        foreach ($x in $names)
        {
            $name = $x.Name
            Write-Output "Hello $name"
        }
}
else {
    Write-Output "Hello World!"
}
```

Ensuite, enregistrez et publiez le runbook révisé. Les exemples ci-dessous montrent comment créer un webhook à l’aide du portail Azure, de PowerShell et de REST.

### <a name="from-the-portal"></a>À partir du portail

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sur le Portail Azure, accédez à votre compte Automation.

1. Sous **Automation du processus**, cliquez sur **Runbooks** pour ouvrir la page **Runbooks**.

1. Sélectionnez votre runbook dans la liste pour ouvrir la page **Vue d’ensemble** du runbook.

1. Sélectionnez **Ajouter un webhook** pour ouvrir la page **Ajouter un webhook**.

   :::image type="content" source="media/automation-webhooks/add-webhook-icon.png" alt-text="Page Vue d’ensemble du runbook avec l’option Ajouter un webhook mise en évidence":::

1. Dans la page **Ajouter un webhook**, sélectionnez **Créer un webhook**.

   :::image type="content" source="media/automation-webhooks/add-webhook-page-create.png" alt-text="Page Ajouter un webhook avec l’option de création mise en évidence":::

1. Entrez le **nom** du webhook. La date d’expiration par défaut pour le champ **Expire** est d’un an à partir de la date actuelle.

1. Cliquez sur l’icône de copie ou appuyez sur <kbd>Ctrl + C</kbd> pour copier l’URL du webhook. Ensuite, enregistrez l’URL en lieu sûr.

    :::image type="content" source="media/automation-webhooks/create-new-webhook.png" alt-text="Page Créer un webhook avec l’URL mise en évidence":::

    > [!IMPORTANT]
    > Une fois que vous avez créé le webhook,vous ne pouvez plus récupérer l’URL. Veillez à le copier et à l’enregistrer comme ci-dessus.

1. Sélectionnez **OK** pour revenir à la page **Ajouter un webhook**.

1. Dans la page **Ajouter un webhook**, sélectionnez **Configurer les paramètres et les paramètres d’exécution** pour ouvrir la page **Paramètres**.

   :::image type="content" source="media/automation-webhooks/add-webhook-page-parameters.png" alt-text="Page Ajouter un webhook avec les paramètres mis en évidence":::

1. Passez en revue la page **Paramètres**. Pour l’exemple de runbook utilisé dans cet article, aucune modification n’est nécessaire. Sélectionnez **OK** pour revenir à la page **Ajouter un webhook**.

1. Dans la page **Ajouter un webhook**, sélectionnez **Créer**. Le webhook est créé et vous êtes redirigé vers la page **Vue d’ensemble** du runbook.

### <a name="using-powershell"></a>Utilisation de PowerShell

1. Vérifiez que vous avez la dernière version du [module Az](/powershell/azure/new-azureps-module-az) de PowerShell.

1. Connectez-vous à Azure de manière interactive à l’aide de l’applet de commande [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) et suivez les instructions.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. Utilisez l’applet de commande [New-AzAutomationWebhook](/powershell/module/az.automation/new-azautomationwebhook) pour créer un webhook pour un runbook Automation. Fournissez une valeur appropriée pour les variables, puis exécutez le script.

    ```powershell
    # Initialize variables with your relevant values
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $runbook = "runbookName"
    $psWebhook = "webhookName"
    
    # Create webhook
    $newWebhook = New-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook `
        -RunbookName $runbook `
        -IsEnabled $True `
        -ExpiryTime "12/31/2022" `
        -Force
    
    # Store URL in variable; reveal variable
    $uri = $newWebhook.WebhookURI
    $uri
    ```

   La sortie est une URL qui ressemble à : `https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`

1. Vous pouvez également vérifier le webhook avec l’applet de commande PowerShell [Get-AzAutomationWebhook](/powershell/module/az.automation/get-azautomationwebhook).

    ```powershell
    Get-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

### <a name="using-rest"></a>Utilisation de REST

La commande PUT est documentée dans [Webhook - Create ou Update](/rest/api/automation/webhook/create-or-update). Cet exemple utilise l’applet de commande PowerShell [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) pour envoyer la demande PUT.

1. Créez un fichier appelé `webhook.json` et collez-y le code suivant :

    ```json
    {
      "name": "RestWebhook",
      "properties": {
        "isEnabled": true,
        "expiryTime": "2022-03-29T22:18:13.7002872Z",
        "runbook": {
          "name": "runbookName"
        }
      }
    }
    ```

   Avant d’exécuter, modifiez la valeur de la propriété **runbook:name** en spécifiant le vrai nom de votre runbook. Passez en revue [Propriétés des webhooks](#webhook-properties) pour plus d’informations sur ces propriétés.

1. Vérifiez que vous avez la dernière version du [module Az](/powershell/azure/new-azureps-module-az) de PowerShell.

1. Connectez-vous à Azure de manière interactive à l’aide de l’applet de commande [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) et suivez les instructions.

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
    # Initialize variables
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $runbook = "runbookName"
    $restWebhook = "webhookName"
    $file = "path\webhook.json"

    # consume file
    $body = Get-Content $file
    
    # Craft Uri
    $restURI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount/webhooks/$restWebhook`?api-version=2015-10-31"
    ```

1. Exécutez le script suivant pour obtenir un jeton d’accès. Si votre jeton d’accès a expiré, vous devez réexécuter le script.

    ```powershell
    # Obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    ```

1. Exécutez le script suivant pour créer le webhook à l’aide de l’API REST.

    ```powershell
    # Invoke the REST API
    # Store URL in variable; reveal variable
    $response = Invoke-RestMethod -Uri $restURI -Method Put -Headers $authHeader -Body $body
    $webhookURI = $response.properties.uri
    $webhookURI
    ```

   La sortie est une URL qui ressemble à : `https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`

1. Vous pouvez également utiliser [Webhook - Get](/rest/api/automation/webhook/get) pour récupérer le webhook identifié par son nom. Vous pouvez exécuter les commandes PowerShell suivantes :

    ```powershell
    $response = Invoke-RestMethod -Uri $restURI -Method GET -Headers $authHeader
    $response | ConvertTo-Json
    ```

## <a name="use-a-webhook"></a>Utiliser un webhook

Cet exemple utilise l’applet de commande PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) pour envoyer la demande POST à votre nouveau webhook.

1. Préparez les valeurs à passer au runbook en tant que corps de l’appel de webhook. Pour des valeurs relativement simples, vous pouvez écrire le script des valeurs comme suit :

    ```powershell
    $Names  = @(
                @{ Name="Hawaii"},
                @{ Name="Seattle"},
                @{ Name="Florida"}
            )
    
    $body = ConvertTo-Json -InputObject $Names
    ```

1. Pour de plus grandes séries de valeurs, vous pouvez utiliser un fichier. Créez un fichier nommé `names.json` et collez-y le code suivant :

    ```json
    [
        { "Name": "Hawaii" },
        { "Name": "Florida" },
        { "Name": "Seattle" }
    ]
    ```

    Remplacez la valeur de la variable `$file` par le vrai chemin du fichier JSON avant d’exécuter les commandes PowerShell suivantes.

    ```powershell
    # Revise file path with actual path
    $file = "path\names.json"
    $bodyFile = Get-Content -Path $file 
    ```

1. Exécutez les commandes PowerShell suivantes pour appeler le webhook à l’aide de l’API REST.

    ```powershell
    $response = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $body -UseBasicParsing
    $response
    
    $responseFile = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $bodyFile -UseBasicParsing
    $responseFile
    ```

   À des fins d’illustration, deux appels ont été effectués pour les deux méthodes différentes de production du corps. Pour la production, utilisez une seule méthode.  La sortie doit ressembler à ce qui suit (une seule sortie est affichée) :

   :::image type="content" source="media/automation-webhooks/webhook-post-output.png" alt-text="Sortie de l’appel de webhook":::

    Le client reçoit de la requête `POST` l’un des codes de retour suivants.

    | Code | Texte | Description |
    |:--- |:--- |:--- |
    | 202 |Acceptée |La requête a été acceptée et le Runbook a été mis en file d'attente avec succès. |
    | 400 |Demande incorrecte |La demande n’a pas été acceptée pour l’une des raisons suivantes : <ul> <li>Le webhook a expiré.</li> <li>Le webhook est désactivé.</li> <li>Le jeton de l’URL n’est pas valide.</li>  </ul> |
    | 404 |Introuvable |La demande n’a pas été acceptée pour l’une des raisons suivantes : <ul> <li>Le webhook est introuvable.</li> <li>Le runbook est introuvable.</li> <li>Le compte est introuvable.</li>  </ul> |
    | 500 |Erreur interne du serveur |L'URL est valide, mais une erreur s'est produite. Soumettez à nouveau la demande. |

    Si la requête aboutit, la réponse webhook contiendra l’ID de travail au format JSON, comme illustré ci-dessous. Elle contiendra un seul ID de travail. Toutefois, le format JSON permettra des améliorations ultérieures potentielles.

    ```json
    {"JobIds":["<JobId>"]}
    ```

1. L’applet de commande PowerShell [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) sera utilisée pour récupérer la sortie. L’[API Azure Automation](/rest/api/automation/job) peut également être utilisée.

    ```powershell
    #isolate job ID
    $jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
    
    # Get output
    Get-AzAutomationJobOutput `
        -AutomationAccountName $automationAccount `
        -Id $jobid `
        -ResourceGroupName $resourceGroup `
        -Stream Output
    ```

   Le résultat doit être semblable à ce qui suit :

   :::image type="content" source="media/automation-webhooks/webhook-job-output.png" alt-text="Sortie du travail de webhook.":::

## <a name="update-a-webhook"></a>Mettre à jour un webhook

Lorsqu’un webhook est créé, il reste valide pendant 10 ans, puis expire automatiquement. Une fois que le webhook a expiré, vous ne pouvez pas le réactiver. Vous pouvez uniquement le supprimer, puis le recréer. Vous pouvez prolonger la durée de validité d’un webhook qui n’a pas encore expiré. Pour étendre un webhook, effectuez les étapes suivantes.

1. Accédez au runbook qui contient le webhook.
1. Sous **Ressources**, sélectionnez **Webhooks**, puis le webhook que vous voulez étendre.
1. Dans la page **Webhook**, choisissez une nouvelle date et une nouvelle heure d’expiration, puis sélectionnez **Enregistrer**.

Passez en revue l’appel d’API [Webhook - Update](/rest/api/automation/webhook/update) et l’applet de commande PowerShell [Set-AzAutomationWebhook](/powershell/module/az.automation/set-azautomationwebhook) pour d’autres modifications possibles.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Voici des exemples de suppression d’un webhook d’un runbook Automation.

- Avec PowerShell, vous pouvez utiliser l’applet de commande [Remove-AzAutomationWebhook](/powershell/module/az.automation/remove-azautomationwebhook) comme indiqué ci-dessous. Aucune sortie n’est retournée.

    ```powershell
    Remove-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

- Avec REST, vous pouvez utiliser l’API REST [Webhook - Delete](/rest/api/automation/webhook/delete) comme indiqué ci-dessous.

    ```powershell
    Invoke-WebRequest -Method Delete -Uri $restURI -Headers $authHeader
    ```

   Une sortie de `StatusCode        : 200` signifie que la suppression a réussi.

## <a name="create-runbook-and-webhook-with-arm-template"></a>Créer un runbook et un webhook avec un modèle ARM

Il est également possible de créer des webhooks Automation à l’aide des modèles [Azure Resource Manager](../azure-resource-manager/templates/overview.md). Cet exemple de modèle crée un compte Automation, quatre runbooks et un webhook pour le runbook nommé.

1. Créez un fichier nommé `webhook_deploy.json` et collez-y le code suivant :

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "automationAccountName": {
                "type": "String",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "webhookName": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Name"
                }
            },
            "runbookName": {
                "type": "String",
                "metadata": {
                    "description": "Runbook Name for which webhook will be created"
                }
            },
            "WebhookExpiryTime": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Expiry time"
                }
            },
            "_artifactsLocation": {
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/",
                "type": "String",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            }
        },
        "resources": [
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "sku": {
                        "name": "Free"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('runbookName')]",
                        "location": "[resourceGroup().location]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "Sample Runbook",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "webhooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('webhookName')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]",
                            "[parameters('runbookName')]"
                        ],
                        "properties": {
                            "isEnabled": true,
                            "expiryTime": "[parameters('WebhookExpiryTime')]",
                            "runbook": {
                                "name": "[parameters('runbookName')]"
                            }
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "webhookUri": {
                "type": "String",
                "value": "[reference(parameters('webhookName')).uri]"
            }
        }
    }
    ```

1. L’exemple de code PowerShell suivant déploie le modèle à partir de votre machine. Fournissez une valeur appropriée pour les variables, puis exécutez le script.

    ```powershell
    $resourceGroup = "resourceGroup"
    $templateFile = "path\webhook_deploy.json"
    $armAutomationAccount = "automationAccount"
    $armRunbook = "ARMrunbookName"
    $armWebhook = "webhookName"
    $webhookExpiryTime = "12-31-2022"
    
    New-AzResourceGroupDeployment `
        -Name "testDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $armAutomationAccount `
        -runbookName $armRunbook `
        -webhookName $armWebhook `
        -WebhookExpiryTime $webhookExpiryTime
    ```

   > [!NOTE]
   > Pour des raisons de sécurité, l’URI n’est retourné que la première fois qu’un modèle est déployé.

## <a name="next-steps"></a>Étapes suivantes

* Pour déclencher un runbook à partir d’une alerte, consultez [Utiliser une alerte pour déclencher un runbook Azure Automation](automation-create-alert-triggered-runbook.md).