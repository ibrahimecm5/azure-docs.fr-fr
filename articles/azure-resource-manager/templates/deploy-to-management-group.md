---
title: Déployer des ressources dans un groupe d’administration
description: Décrit comment déployer des ressources au niveau du groupe d’administration dans un modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/14/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: ea8ee72cebc8a44d3e87ab80ab22d04b4fdc9f66
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319308"
---
# <a name="management-group-deployments-with-arm-templates"></a>Déploiements de groupes d’administration avec des modèles Resource Manager

À mesure que votre organisation arrive mûrit, vous pouvez déployer un modèle Azure Resource Manager (modèle ARM) pour créer des ressources au niveau du groupe d’administration. Par exemple, vous pouvez être amené à définir et attribuer des [stratégies](../../governance/policy/overview.md) ou un [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md) pour un groupe d’administration. Avec des modèles au niveau du groupe d’administration, vous pouvez de façon déclarative appliquer des stratégies et assigner des rôles au niveau du groupe d’administration.

## <a name="supported-resources"></a>Ressources prises en charge

Certains types de ressources ne peuvent pas être déployés au niveau du groupe d’administration. Cette section répertorie les types de ressources pris en charge.

Pour Azure Blueprints, utilisez :

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

Pour Azure Policy, utilisez :

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Pour le contrôle d’accès en fonction du rôle Azure (Azure RBAC), utilisez :

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Pour les modèles imbriqués qui sont déployés sur des abonnements ou des groupes de ressources, utilisez :

* [deployments](/azure/templates/microsoft.resources/deployments)

Pour gérer vos ressources, utilisez :

* [balises](/azure/templates/microsoft.resources/tags)

Les groupes d’administration sont des ressources de niveau client. Toutefois, vous pouvez créer des groupes d’administration dans un déploiement de groupe d’administration en définissant l’étendue du nouveau groupe d’administration sur le locataire. Consultez [Groupe d’administration](#management-group).

## <a name="schema"></a>schéma

Le schéma que vous utilisez pour les déploiements au niveau du groupe d'administration est différent de celui utilisé pour les déploiements de groupes de ressources.

Pour les modèles, utilisez :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  ...
}
```

Le schéma d’un fichier de paramètres est le même pour toutes les étendues de déploiement. Fichiers de fichiers de paramètres, utilisez :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  ...
}
```

## <a name="deployment-commands"></a>Commandes de déploiement

Pour opérer un déploiement vers un groupe d’administration, utilisez les commandes de déploiement de groupes d’administration.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour Azure CLI, utilisez [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create) :

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour Azure PowerShell, utilisez [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Pour plus d’informations sur les commandes et options de déploiement de modèles Resource Manager, consultez :

* [Déployer des ressources avec des modèles ARM et le Portail Azure](deploy-portal.md)
* [Déployer des ressources à l’aide de modèles ARM et l’interface CLI Azure](deploy-cli.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](deploy-powershell.md)
* [Déployer des ressources avec des modèles Resource Manager et l’API REST Azure Resource Manager](deploy-rest.md)
* [Utiliser un bouton de déploiement pour déployer des modèles à partir du référentiel GitHub](deploy-to-azure-button.md)
* [Déployer des modèles Resource Manager à partir de Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Emplacement et nom du déploiement

Pour les déploiements au niveau du groupe d'administration, vous devez fournir un emplacement de déploiement. L’emplacement du déploiement est distinct de l’emplacement des ressources que vous déployez. L’emplacement de déploiement indique où stocker les données de déploiement. Les déploiements d’[abonnement](deploy-to-subscription.md) et de [locataire](deploy-to-tenant.md) nécessitent également un emplacement. Pour les déploiements d’un [groupe de ressources](deploy-to-resource-group.md), l’emplacement du groupe de ressources est utilisé pour stocker les données de déploiement.

Vous pouvez fournir un nom de déploiement ou utiliser le nom de déploiement par défaut. Le nom par défaut est le nom du fichier de modèle. Par exemple, le déploiement d’un modèle nommé _azuredeploy.json_ crée le nom de déploiement par défaut **azuredeploy**.

Pour chaque nom de déploiement, l’emplacement est immuable. Il n’est pas possible de créer un déploiement dans un emplacement s’il existe un déploiement du même nom dans un autre emplacement. Par exemple, si vous créez un déploiement de groupe d’administration avec le nom **deployment1** dans **centralus**, vous ne pouvez pas créer par la suite un autre déploiement avec le nom **deployment1** mais un emplacement **westus**. Si vous obtenez le code d’erreur `InvalidDeploymentLocation`, utilisez un autre nom ou le même emplacement que le déploiement précédent pour ce nom.

## <a name="deployment-scopes"></a>Étendues de déploiement

Lors du déploiement sur un groupe d’administration, vous pouvez déployer des ressources vers :

* le groupe d’administration cible de l’opération
* un autre groupe d’administration dans le locataire
* des abonnements dans le groupe d’administration
* les groupes de ressources dans le groupe d’administration
* le locataire pour le groupe de ressources

Une [ressource d’extension](scope-extension-resources.md) peut être étendue à une cible différente de la cible de déploiement.

L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

Cette section montre comment spécifier des étendues différentes. Vous pouvez combiner ces différentes étendues dans un seul modèle.

### <a name="scope-to-target-management-group"></a>Étendue au groupe d’administration cible

Les ressources définies dans la section des ressources du modèle sont appliquées au groupe d’administration à partir de la commande de déploiement.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Étendue à un autre groupe d’administration

Pour cibler un autre groupe d’administration, ajoutez un déploiement imbriqué et spécifiez la propriété `scope`. Définissez la propriété `scope` sur une valeur au format `Microsoft.Management/managementGroups/<mg-name>`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Étendue à l’abonnement

Vous pouvez également cibler des abonnements dans un groupe d’administration. L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

Pour cibler un abonnement au sein du groupe d’administration, utilisez un déploiement imbriqué et la propriété `subscriptionId`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Étendue au groupe de ressources

Vous pouvez également cibler des groupes de ressources au sein du groupe d’administration. L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

Pour cibler un groupe de ressources au sein du groupe d’administration, utilisez un déploiement imbriqué. Définissez les propriétés `subscriptionId` et `resourceGroup`. Ne définissez pas un emplacement pour le déploiement imbriqué, car il est déployé à l’emplacement du groupe de ressources.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Pour utiliser un déploiement de groupe d’administration afin de créer un groupe de ressources dans un abonnement et de déployer un compte de stockage dans ce groupe de ressources, consultez [Déployer dans un abonnement et un groupe de ressources](#deploy-to-subscription-and-resource-group).

### <a name="scope-to-tenant"></a>Étendue au locataire

Pour créer des ressources au niveau du locataire, définissez `scope` sur `/`. L’utilisateur qui déploie le modèle doit disposer de l’[accès requis pour déployer au niveau du locataire](deploy-to-tenant.md#required-access).

Pour utiliser un déploiement imbriqué, définissez `scope` et `location`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

Ou vous pouvez définir l’étendue sur `/` pour certains types de ressources, comme les groupes d’administration. La création d’un nouveau groupe d’administration est décrite dans la section suivante.

## <a name="management-group"></a>Groupe d’administration

Pour créer un groupe d’administration dans un déploiement de groupe d’administration, vous devez définir l’étendue sur `/` pour le groupe d’administration.

L’exemple suivant crée un groupe d’administration dans le groupe d’administration racine.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

L’exemple d’après crée un nouveau groupe d’administration dans le groupe d’administration spécifié comme parent. Notez que l’étendue est définie sur `/`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    },
    "parentMG": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('mgName')]",
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2021-04-01",
      "scope": "/",
      "location": "eastus",
      "properties": {
        "details": {
          "parent": {
            "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
          }
        }
      }
    }
  ],
  "outputs": {
    "output": {
      "type": "string",
      "value": "[parameters('mgName')]"
    }
  }
}
```

## <a name="subscriptions"></a>Abonnements

Pour utiliser un modèle ARM afin de créer un nouvel abonnement Azure dans un groupe d’administration, consultez :

* [Créer des abonnements Contrat Entreprise Azure par programmation](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Créer des abonnements Azure pour un Contrat client Microsoft par programmation](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Créer des abonnements Azure pour un Contrat Partenaire Microsoft par programmation](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Pour déployer un modèle qui déplace un abonnement Azure existant vers un nouveau groupe d’administration, consultez [Déplacer des abonnements dans un modèle ARM](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Azure Policy

Les définitions de stratégie personnalisée qui sont déployées sur un groupe d’administration sont des extensions de celui-ci. Pour obtenir l’ID d’une définition de stratégie personnalisée, utilisez la fonction [extensionResourceId()](template-functions-resource.md#extensionresourceid). Les définitions de stratégie intégrées sont des ressources de niveau locataire. Pour obtenir l’ID d’une définition de stratégie intégrée, utilisez la fonction [tenantResourceId()](template-functions-resource.md#tenantresourceid).

L’exemple suivant montre comment [définir](../../governance/policy/concepts/definition-structure.md) une stratégie au niveau du groupe d’administration et l’attribuer.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "targetMG": {
      "type": "string",
      "metadata": {
        "description": "Target Management Group"
      }
    },
    "allowedLocations": {
      "type": "array",
      "defaultValue": [
        "australiaeast",
        "australiasoutheast",
        "australiacentral"
      ],
      "metadata": {
        "description": "An array of the allowed locations, all other locations will be denied by the created policy."
      }
    }
  },
  "variables": {
    "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
    "policyDefinition": "LocationRestriction"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "name": "[variables('policyDefinition')]",
      "apiVersion": "2020-09-01",
      "properties": {
        "policyType": "Custom",
        "mode": "All",
        "parameters": {
        },
        "policyRule": {
          "if": {
            "not": {
              "field": "location",
              "in": "[parameters('allowedLocations')]"
            }
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "location-lock",
      "apiVersion": "2020-09-01",
      "dependsOn": [
        "[variables('policyDefinition')]"
      ],
      "properties": {
        "scope": "[variables('mgScope')]",
        "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
      }
    }
  ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Déployer dans un abonnement et un groupe de ressources

À partir d’un déploiement au niveau du groupe d’administration, vous pouvez cibler un abonnement au sein du groupe d’administration. L’exemple suivant crée un groupe de ressources à l’intérieur d’un abonnement et déploie un compte de stockage sur ce groupe de ressources.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2021-04-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedRG",
      "subscriptionId": "[parameters('nestedSubId')]",
      "resourceGroup": "[parameters('nestedRG')]",
      "dependsOn": [
        "nestedSub"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2021-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('nestedLocation')]",
              "kind": "StorageV2",
              "sku": {
                "name": "Standard_LRS"
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’attribution de rôles, consultez [Attribuer des rôles Azure avec des modèles Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Pour obtenir un exemple de déploiement des paramètres d’espace de travail pour Microsoft Defender pour le cloud, consultez [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Vous pouvez également déployer des modèles au [niveau de l’abonnement](deploy-to-subscription.md) et au [niveau du locataire](deploy-to-tenant.md).
