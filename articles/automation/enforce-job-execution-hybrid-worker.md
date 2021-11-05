---
title: Forcer l'exécution d'un travail sur un Runbook Worker hybride Azure Automation
description: Cet article explique comment utiliser une définition Azure Policy personnalisée pour forcer l'exécution d'un travail sur un Runbook Worker hybride Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 05/24/2021
ms.topic: conceptual
ms.openlocfilehash: ced119e7835f6c7fd7bf6aea34a10ebea4af3fc5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026276"
---
# <a name="use-azure-policy-to-enforce-job-execution-on-hybrid-runbook-worker"></a>Utiliser Azure Policy pour forcer l'exécution d'un travail sur un Runbook Worker hybride

Le démarrage d'un runbook sur un Runbook Worker hybride s'effectue à l'aide d'une option **Exécuter sur** qui vous permet de spécifier le nom d'un groupe Runbook Worker hybride lors d'un lancement à partir du portail Azure, d'Azure PowerShell ou de l'API REST. Quand un groupe est spécifié, l’un des Workers de ce groupe récupère et exécute le runbook. Si votre runbook ne spécifie pas cette option, Azure Automation l'exécute dans le bac à sable Azure. 

Toute personne de votre organisation qui dispose du rôle d'[Opérateur de travaux Automation](automation-role-based-access-control.md#automation-job-operator) ou d'un rôle supérieur peut créer des travaux de runbook. Pour gérer l'exécution d'un runbook en ciblant un groupe Runbook Worker hybride de votre compte Automation, vous pouvez utiliser [Azure Policy](../governance/policy/overview.md). Cela permet de faire respecter les normes internes et de s'assurer que les travaux d'automatisation sont contrôlés et gérés par les personnes désignées. Et n'importe qui ne peut pas exécuter un runbook sur un bac à sable Azure ; l'exécution est uniquement possible sur des Runbook Workers hybrides. 

Cet article contient une définition Azure Policy personnalisée pour vous aider à contrôler ces activités à l'aide des opérations suivantes de l'API REST Automation. Plus précisément :

* [Créer un travail](/rest/api/automation/job/create)
* [Créer un calendrier des travaux](/rest/api/automation/jobschedule/create)
* [Créer un webhook](/rest/api/automation/webhook/createorupdate)

Cette stratégie est basée sur la propriété `runOn`. La stratégie valide la valeur de la propriété, qui doit contenir le nom d'un groupe Runbook Worker hybride existant. Si la valeur est Null, elle est interprétée comme la demande de création du travail, du calendrier des travaux, ou du webhook si elle est destinée au bac à sable Azure, et la demande est refusée.

## <a name="permissions-required"></a>Autorisations requises

Vous devez être [Propriétaire](../role-based-access-control/built-in-roles.md#owner) de l'abonnement pour être autorisé à accéder aux ressources Azure Policy.

## <a name="create-and-assign-the-policy-definition"></a>Créer et attribuer la définition de stratégie

Nous allons ici composer la règle de stratégie, puis l'attribuer à un groupe d'administration ou à un abonnement, après quoi nous pourrons éventuellement spécifier un groupe de ressources dans l'abonnement. Si vous n’êtes pas encore familiarisé avec le langage de stratégie, consultez la [structure de définition de stratégie](../governance/policy/concepts/definition-structure.md) pour savoir comment structurer la définition de stratégie.

1. Utilisez l'extrait de code JSON suivant pour créer un fichier JSON nommé AuditAutomationHRWJobExecution.json.

    ```json
    {
        "properties": {
            "displayName": "Enforce job execution on Automation Hybrid Runbook Worker",
            "description": "Enforce job execution on Hybrid Runbook Workers in your Automation account.",
            "mode": "all",
            "parameters": {
                "effectType": {
                    "type": "string",
                    "defaultValue": "Deny",
                    "allowedValues": [
                       "Deny",
                       "Disabled"
                    ],
                    "metadata": {
                        "displayName": "Effect",
                        "description": "Enable or disable execution of the policy"
                    }
                }
            },
            "policyRule": {
          "if": {
            "anyOf": [
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobs"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/webhooks"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/webhooks/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobSchedules"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobSchedules/runOn'))]",
                    "less": 1
                  }
                ]
              }
            ]
          },
          "then": {
            "effect": "[parameters('effectType')]"
          }
        }
      }
    }
    ```

2. Exécutez la commande Azure PowerShell ou Azure CLI suivante pour créer une définition de stratégie à l'aide du fichier AuditAutomationHRWJobExecution.json.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az policy definition create --name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' --display-name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' --description 'This policy enforces job execution on Automation account user Hybrid Runbook Workers.' --rules 'AuditAutomationHRWJobExecution.json' --mode All
    ```

    La commande crée une définition de stratégie nommée **Audit Enforce Jobs on Automation Hybrid Runbook Workers**. Pour plus d’informations sur les autres paramètres utilisables, consultez [az policy definition create](/cli/azure/policy/definition#az_policy_definition_create).

    Lorsqu’il est appelé sans paramètre d’emplacement, par défaut, `az policy definition create` enregistre la définition de stratégie dans l’abonnement sélectionné du contexte de sessions. Pour enregistrer la définition dans un autre emplacement, utilisez les paramètres suivants :

    * **subscription** : enregistrer dans un autre abonnement. Nécessite une valeur *GUID* pour l’ID d’abonnement ou une valeur *chaîne* pour le nom de l’abonnement.
    * **--management-group** : enregistrer dans un groupe d’administration. Une valeur de *chaîne* est nécessaire.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -DisplayName 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' -Policy 'AuditAutomationHRWJobExecution.json'
    ```

    La commande crée une définition de stratégie nommée **Audit Enforce Jobs on Automation Hybrid Runbook Workers**. Pour plus d'informations sur les autres paramètres que vous pouvez utiliser, consultez [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

    Lorsqu’il est appelé sans paramètre d’emplacement, par défaut, `New-AzPolicyDefinition` enregistre la définition de stratégie dans l’abonnement sélectionné du contexte de sessions. Pour enregistrer la définition dans un autre emplacement, utilisez les paramètres suivants :

    * **SubscriptionId** : enregistrer dans un autre abonnement. Une valeur *GUID* est nécessaire.
    * **ManagementGroupName** : enregistrer dans un groupe d’administration. Une valeur de *chaîne* est nécessaire.

    ---

3. Après avoir créé votre définition de stratégie, vous pouvez créer une attribution de stratégie en exécutant les commandes suivantes :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
    ```

    Le paramètre **Scope** sur `az policy assignment create` peut être défini pour un groupe d’administration, un abonnement, un groupe de ressources ou une seule ressource. Le paramètre utilise un chemin de ressource complet. Pour chaque conteneur, le modèle pour **scope** est le suivant. Remplacez `{rName}`, `{rgName}`, `{subId}` et `{mgName}` par le nom de la ressource, le nom du groupe de ressources, l’ID de l’abonnement et le nom du groupe d’administration, respectivement. `{rType}` est remplacé par le **type de la ressource**, comme `Microsoft.Compute/virtualMachines` pour une machine virtuelle.

    - Ressource : `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - Groupe de ressources : `/subscriptions/{subID}/resourceGroups/{rgName}`
    - Abonnement : `/subscriptions/{subID}`
    - Groupe d'administration : `/providers/Microsoft.Management/managementGroups/{mgName}`

    Vous pouvez obtenir l’ID de définition de stratégie Azure à l’aide de PowerShell avec la commande suivante :
    
    ```azurecli
    az policy definition show --name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers'
    ```
    
    L’ID de définition de stratégie pour la définition de stratégie que vous avez créée doit ressembler à ce qui suit :
    
    ```output
    "/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Enforce Jobs on Automation Hybrid Runbook Workers"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $rgName = Get-AzResourceGroup -Name 'ContosoRG'
    $Policy = Get-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers'
    New-AzPolicyAssignment -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -PolicyDefinition $Policy -Scope $rg.ResourceId
    ```

    Remplacez _ContosoRG_ par le nom de votre groupe de ressources prévu.

    Le paramètre **Scope** sur `New-AzPolicyAssignment` peut être défini pour un groupe d’administration, un abonnement, un groupe de ressources ou une seule ressource. Le paramètre utilise un chemin d’accès de ressource complet, que la propriété **ResourceId** renvoie sur `Get-AzResourceGroup`. Pour chaque conteneur, le modèle **Étendue** est le suivant. Remplacez `{rName}`, `{rgName}`, `{subId}` et `{mgName}` par le nom de la ressource, le nom du groupe de ressources, l’ID de l’abonnement et le nom du groupe d’administration, respectivement.
    `{rType}` est remplacé par le **type de la ressource**, comme `Microsoft.Compute/virtualMachines` pour une machine virtuelle.

    - Ressource : `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - Groupe de ressources : `/subscriptions/{subId}/resourceGroups/{rgName}`
    - Abonnement : `/subscriptions/{subId}`
    - Groupe d'administration : `/providers/Microsoft.Management/managementGroups/{mgName}`

    ---

4. Connectez-vous au [Portail Azure](https://portal.azure.com).
5. Lancez le service Azure Policy dans le portail Azure en sélectionnant **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.
6. Sélectionnez **Conformité** dans la partie gauche de la page. Recherchez ensuite l'attribution de stratégie que vous avez créée.

   :::image type="content" source="./media/enforce-job-execution-hybrid-worker/azure-policy-dashboard-policy-status.png" alt-text="Capture d'écran du tableau de bord Azure Policy.":::

Lorsque l'une des opérations REST Automation est exécutée sans référence à un Runbook Worker hybride dans le corps de la demande, un code de réponse 403 est renvoyé avec une erreur semblable à l'exemple suivant pour indiquer que l'opération a tenté de s'exécuter sur un bac à sable Azure :

```rest
{
  "error": {
    "code": "RequestDisallowedByPolicy",
    "target": "Start_VMS",
    "message": "Resource 'Start_VMS' was disallowed by policy. Policy identifiers: '[{\"policyAssignment\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917\"},\"policyDefinition\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc\"}}]'.",
    "additionalInfo": [
      {
        "type": "PolicyViolation",
        "info": {
          "policyDefinitionDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "evaluationDetails": {
            "evaluatedExpressions": [
              {
                "result": "True",
                "expressionKind": "Field",
                "expression": "type",
                "path": "type",
                "expressionValue": "Microsoft.Automation/automationAccounts/jobs",
                "targetValue": "Microsoft.Automation/automationAccounts/jobs",
                "operator": "Equals"
              },
              {
                "result": "True",
                "expressionKind": "Value",
                "expression": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                "expressionValue": 0,
                "targetValue": 1,
                "operator": "Less"
              }
            ]
          },
          "policyDefinitionId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionName": "4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionEffect": "Deny",
          "policyAssignmentId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917",
          "policyAssignmentName": "fd5e2cb3842d4eefbc857917",
          "policyAssignmentDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "policyAssignmentScope": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG",
          "policyAssignmentParameters": {}
        }
      }
    ]
  }
}
```

La tentative d'opération est également enregistrée dans le journal d'activité du compte Automation, comme dans l'exemple suivant.

:::image type="content" source="./media/enforce-job-execution-hybrid-worker/failed-job-activity-log-example.png" alt-text="Exemple de journal d'activité suite à l'échec de l'exécution d'un travail.":::

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser des runbooks, voir [Gérer les runbooks dans Azure Automation](manage-runbooks.md).