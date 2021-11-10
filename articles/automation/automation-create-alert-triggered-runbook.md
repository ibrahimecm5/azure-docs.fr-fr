---
title: Utiliser une alerte pour déclencher un runbook Azure Automation
description: Cet article explique comment déclencher un runbook à exécuter quand une alerte Azure est levée.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2584390610aaef5ddd8364d33f3c065ecf3bd321
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443829"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Utiliser une alerte pour déclencher un runbook Azure Automation

Vous pouvez utiliser [Azure Monitor](../azure-monitor/overview.md) pour surveiller des métriques de base et des journaux d’activité pour la plupart des services Azure. Vous pouvez appeler les runbooks Azure Automation en utilisant des [groupes d’actions](../azure-monitor/alerts/action-groups.md) pour automatiser des tâches basées sur des alertes. Cet article explique comment configurer et exécuter un runbook en utilisant des alertes.


## <a name="prerequisites"></a>Prérequis

* Un compte Azure Automation avec au moins une identité managée affectée par l’utilisateur. Pour plus d’informations, consultez [Utilisation d’une identité managée affectée par l’utilisateur pour un compte Azure Automation](./add-user-assigned-identity.md).
* Modules Az : `Az.Accounts` et `Az.Compute` importés dans le compte Automation. Pour plus d’informations, consultez [Importer des modules Az](./shared-resources/modules.md#import-az-modules).
* Une [machine virtuelle Azure](../virtual-machines/windows/quick-create-powershell.md).
* Le [module PowerShell Azure Az](/powershell/azure/new-azureps-module-az) installé sur votre ordinateur. Pour plus d'informations sur son installation ou sa mise à niveau, consultez [Installer le module PowerShell Azure Az](/powershell/azure/install-az-ps).
* Une connaissance générale des [runbooks Automation](./manage-runbooks.md).

## <a name="alert-types"></a>Types d’alertes

Vous pouvez utiliser des runbooks Automation avec trois types d’alerte :

* Alertes courantes
* Alertes de journal d’activité
* Alertes de métrique en quasi temps réel

> [!NOTE]
> Le schéma d’alerte commun standardise l’expérience de consommation pour les notifications d’alerte dans Azure. Les trois types d’alerte actuels dans Azure (métrique, journal et journal d’activité) ont traditionnellement eu leurs propres modèles d’e-mail, schémas de webhook, etc. Pour en savoir plus, consultez [Schéma d’alerte courant](../azure-monitor/alerts/alerts-common-schema.md).

Quand une alerte appelle un runbook, l’appel réel est une demande HTTP POST vers le Webhook. Le corps de la demande POST contient un objet au format JSON qui contient les propriétés utiles relatives à l’alerte. Le tableau suivant contient des liens vers le schéma de la charge utile pour chaque type d’alerte :

|Alerte  |Description|Schéma de la charge utile  |
|---------|---------|---------|
|[Alerte courante](../azure-monitor/alerts/alerts-common-schema.md)|Le schéma d’alerte courant qui standardise l’expérience de consommation pour les notifications d’alerte dans Azure de nos jours.|Schéma de la charge utile d’alerte courante|
|[Alerte du journal d’activité](../azure-monitor/alerts/activity-log-alerts.md) |Envoie une notification lorsqu’un nouvel événement du journal d’activité remplit des conditions spécifiques. Par exemple, lorsqu’une opération `Delete VM` est effectuée dans **myProductionResourceGroup** ou lorsqu’un nouvel événement Azure Service Health avec un statut Actif apparaît.| [Schéma de la charge utile et alerte de journal d’activité](../azure-monitor/alerts/activity-log-alerts-webhook.md)     |
|[Alerte de métrique quasiment en temps réel](../azure-monitor/alerts/alerts-metric-near-real-time.md) | Envoie une notification plus rapidement que des alertes de métrique lorsqu’une ou plusieurs mesures au niveau de la plateforme remplissent des conditions spécifiques. Par exemple, lorsque la valeur **CPU %** d’une machine virtuelle est supérieure à 90, et que la valeur **Network In** est supérieure à 500 Mo lors des 5 dernières minutes.| [Schéma de la charge utile et alerte de métrique quasiment en temps réel](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Les données fournies par chaque type d’alerte étant différentes, chaque type d’alerte doit être gérée différemment. Dans la section suivante, vous allez apprendre à créer un runbook pour gérer différents types d’alertes.

## <a name="assign-permissions-to-managed-identities"></a>Attribuer des autorisations aux identités managées

Affectez des autorisations à l’[identité managée](./automation-security-overview.md#managed-identities) appropriée pour lui permettre d’arrêter une machine virtuelle. Le runbook peut utiliser l’identité managée affectée par le système du compte Automation ou une identité managée affectée par l’utilisateur. Des étapes sont fournies pour affecter des autorisations à chaque identité. Les étapes ci-dessous utilisent PowerShell. Si vous préférez utiliser le portail, consultez [Attribuer des rôles Azure à l’aide du portail Azure](./../role-based-access-control/role-assignments-portal.md).

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
    $resourceGroup = "resourceGroup"
    $automationAccount = "AutomationAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    ```

1. Utilisez la cmdlet PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) pour affecter un rôle à l’identité managée affectée par le système.

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. Attribuer un rôle à une identité managée affectée par l'utilisateur.

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity)
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. Pour l’identité managée affectée par le système, affichez `ClientId` et enregistrez la valeur pour une utilisation ultérieure.

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-a-runbook-to-handle-alerts"></a>Créer un runbook pour gérer les alertes

Pour utiliser Automation avec des alertes, vous avez besoin d’un runbook qui gère la charge utile JSON des alertes qui est passée au runbook. L’exemple de runbook suivant doit être appelé à partir d’une alerte Azure.

Comme décrit dans la section précédente, chaque type d’alerte a un schéma différent. Le script recueille les données du wekbhook à partir d'une alerte dans le paramètre d'entrée du runbook `WebhookData`. Il évalue ensuite la charge utile JSON pour déterminer le type d’alerte utilisé.

Cet exemple utilise une alerte à partir d’une machine virtuelle Azure. Il récupère les données de la machine virtuelle à partir de la charge utile, puis utilise ces informations pour arrêter la machine virtuelle. La connexion doit être configurée dans le compte Automation sur lequel le runbook est exécuté. Lorsque vous utilisez des alertes pour déclencher des runbooks, il est important de vérifier l’état de l’alerte dans le runbook déclenché. Le runbook se déclenche chaque fois que l’alerte change d’état. Les alertes présentent plusieurs états dont les deux plus courants sont Activé et Résolu. Recherchez l'état dans la logique de runbook pour vous assurer que le runbook ne s’exécute pas plusieurs fois. L’exemple de cet article montre uniquement comment rechercher des alertes avec état Activé uniquement.

Le runbook utilise l’[identité managée affectée par le système](./automation-security-overview.md#managed-identities) du compte Automation pour s’authentifier auprès d’Azure afin d’effectuer l’action de gestion sur la machine virtuelle. Le runbook peut être facilement modifié pour utiliser une identité managée affectée par l’utilisateur.

Utilisez cet exemple pour créer un runbook appelé **Stop-AzureVmInResponsetoVMAlert**. Vous pouvez modifier le script PowerShell et l’utiliser avec de nombreuses ressources différentes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre compte Automation.

1. Sous **Automatisation de processus**, sélectionnez **Runbooks**.

1. Sélectionnez **+ Créer un runbook**.
    1. Nommez le runbook `Stop-AzureVmInResponsetoVMAlert`.
    1. Dans la liste déroulante **Type de runbook**, sélectionnez **PowerShell**.
    1. Sélectionnez **Create** (Créer).

1. Dans l’éditeur de runbook, collez le code suivant :

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }
    
        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose
    
            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose
    
                # Ensures you do not inherit an AzContext in your runbook
                Disable-AzContextAutosave -Scope Process
     
                # Connect to Azure with system-assigned managed identity
                $AzureContext = (Connect-AzAccount -Identity).context
  
                # set and store context
                $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
    
                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -DefaultProfile $AzureContext -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

1. Si vous souhaitez que le runbook s’exécute avec l’identité managée affectée par le système, laissez le code tel quel. Si vous préférez utiliser une identité managée affectée par l’utilisateur, procédez comme suit :
    1. À la ligne 78, supprimez `$AzureContext = (Connect-AzAccount -Identity).context`,
    1. Remplacez-la par `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` et
    1. Entrez l'ID client que vous avez obtenu précédemment.

1. Sélectionnez **Enregistrer**, **Publier**, puis **Oui** lorsque vous y êtes invité.

1. Fermez la page **Runbook** pour revenir à la page **Compte Automation**.

## <a name="create-the-alert"></a>Créez l’alerte

Les alertes utilisent des groupes d’actions, qui sont des collections d’actions déclenchées par l’alerte. Un runbook n’est qu’une des nombreuses actions que vous pouvez utiliser avec les groupes d’actions.

1. Dans votre compte Automation, sélectionnez **Alertes** sous **Surveillance**.

1. Sélectionnez **+ Nouvelle règle d’alerte** pour ouvrir la page **Créer une règle d’alerte**.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-alert-rule-portal.png" alt-text="Page créer une règle d’alerte et sous-sections.":::

1. Sous **Étendue**, sélectionnez **Modifier la ressource**. 

1. Sur la page **Sélectionner une ressource**, sélectionnez **Machines virtuelles** dans la liste déroulante **Filtrer par type de ressource**.

1. Cochez la case en regard de la ou des machines virtuelles que vous souhaitez analyser. Sélectionnez ensuite **Terminé** pour revenir à la page **Créer une règle d’alerte**.

1. Sous **Condition**, sélectionnez **Ajouter une condition**.

1. Sur la page **Sélectionner un signal**, entrez `Percentage CPU` dans la zone de texte Rechercher, puis sélectionnez **Pourcentage d’UC** dans les résultats.

1. Sur la page **Configurer la logique du signal**, sous **Valeur de seuil**, entrez une valeur basse initiale à des fins de test, telle que `5`. Vous pouvez revenir en arrière et mettre à jour cette valeur une fois que vous avez confirmé que l’alerte fonctionne comme prévu. Sélectionnez ensuite **Terminé** pour revenir à la page **Créer une règle d’alerte**.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-signal-logic-portal.png" alt-text="Entrez la valeur du seuil du pourcentage d’UC.":::
 
1. Sous **Actions**, sélectionnez **Ajouter des groupes d’actions**, puis **+ Créer un groupe d’actions**.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-action-group-portal.png" alt-text="Page créer un groupe d’actions avec l’onglet Concepts de base ouvert.":::

1. Sur la page **Créer un groupe d’actions**:
    1. Sous l’onglet **Concepts de base**, entrez un **Nom de groupe d'actions** et un **Nom d'affichage**.
    1. Sous l’onglet **Actions**, dans la zone de texte **Nom**, entrez un nom. Ensuite, dans la liste déroulante **Type d’action**, sélectionnez **Runbook Automation** pour ouvrir la page **Configurer un runbook**.
        1. Pour l’élément **Source du Runbook**, sélectionnez **Utilisateur**.  
        1. Dans la liste déroulante **Abonnement**, sélectionnez votre abonnement.
        1. Dans la liste déroulante **Compte Automation**, sélectionnez votre compte Automation.
        1. Dans la liste déroulante **Runbook** , sélectionnez **Stop-AzureVmInResponsetoVMAlert**.
        1. Pour l’élément **Activer le schéma d’alerte courant**, sélectionnez **Oui**.
        1. Sélectionnez **OK** pour revenir à la page **Créer un groupe d’actions**.
        
            :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-runbook-portal.png" alt-text="Configurez la page du runbook avec des valeurs.":::

    1. Sélectionnez **Vérifier + créer**, puis **Créer** pour revenir à la page **Créer une règle d’alerte**.

1. Sous **Détails de la règle d’alerte**, pour la zone de texte **Nom de la règle d’alerte**

1. Sélectionnez **Créer une règle d’alerte**.  Vous pouvez utiliser ce groupe d’actions dans les [alertes du journal d’activité](../azure-monitor/alerts/activity-log-alerts.md) et les [alertes quasiment en temps réel](../azure-monitor/alerts/alerts-overview.md) que vous avez créées.

## <a name="verification"></a>Vérification

Vérifier que votre machine virtuelle est en cours d’exécution. Accédez au runbook **Stop-AzureVmInResponsetoVMAlert** et faites attention à la liste des **tâches récentes** à remplir. Une fois qu’une tâche terminée s’affiche, sélectionnez la tâche et passez en revue la sortie. Vérifiez également si votre machine virtuelle s’est arrêtée.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/job-result-portal.png" alt-text="Affichage de la sortie de la tâche.":::


## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir les différentes façons de démarrer un runbook, consultez [Démarrer un runbook](./start-runbooks.md).
* Pour créer une alerte de journal d’activité, consultez [Créer des alertes de journal d’activité](../azure-monitor/alerts/activity-log-alerts.md).
* Pour apprendre à créer une alerte quasiment en temps réel, consultez [Créer une règle d’alerte avec le portail Azure](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json).

