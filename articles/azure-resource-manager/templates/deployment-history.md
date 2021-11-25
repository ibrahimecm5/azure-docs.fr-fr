---
title: Historique de déploiement
description: Décrit comment afficher les opérations de déploiement d’Azure Resource Manager avec le portail, PowerShell, la CLI Azure et l’API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/10/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 60bc06ab15e7f36a5c67ff5fc4de9da8bad28f88
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310218"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Afficher l’historique des déploiements avec Azure Resource Manager

Azure Resource Manager vous permet de voir l’historique de votre déploiement. Vous pouvez examiner des opérations spécifiques dans des déploiements antérieurs et voir quelles ressources ont été déployées. Cet historique contient des informations sur les erreurs éventuelles.

L’historique de déploiement de chaque groupe de ressources est limité à 800 déploiements. Quand vous vous approchez de la limite, les déploiements sont automatiquement supprimés de l’historique. Pour plus d’informations, consultez [Suppressions automatiques de l’historique de déploiement](deployment-history-deletions.md).

Pour obtenir de l’aide sur la résolution d’erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes de déploiement Azure](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Récupérer des déploiements et l’ID de corrélation

Vous pouvez afficher les détails d’un déploiement via le Portail Azure, PowerShell, Azure CLI ou l’API REST. Chaque déploiement a un ID de corrélation, qui est utilisé pour suivre les événements connexes. Si vous [créez une demande de support Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md), le support peut vous demander l’ID de corrélation. Le support utilise l’ID de corrélation pour identifier les opérations du déploiement qui a échoué.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Sélectionnez le groupe de ressources que vous souhaitez examiner.

1. Sélectionnez le lien sous **Déploiements**.

   :::image type="content" source="media/deployment-history/select-deployment-history.png" alt-text="Capture d’écran de la vue d’ensemble du groupe de ressources qui montre un déploiement réussi.":::

1. Sélectionnez un des déploiements dans l’historique des déploiements.

   :::image type="content" source="media/deployment-history/select-details.png" alt-text="Capture d’écran du lien mis en évidence pour un déploiement de ressources.":::

1. Un résumé du déploiement s’affiche, y compris l’ID de corrélation.

   :::image type="content" source="media/deployment-history/show-correlation-id.png" alt-text="Capture d’écran de l’historique de déploiement qui met en évidence l’ID de corrélation.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour répertorier tous les déploiements d’un groupe de ressources, utilisez la commande [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment).

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Pour obtenir un déploiement spécifique à partir d’un groupe de ressources, ajoutez le paramètre `DeploymentName`.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Pour obtenir l’ID de corrélation, utilisez :

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour répertorier tous les déploiements d’un groupe de ressources, utilisez [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Pour obtenir un déploiement spécifique, utilisez [az deployment group show](/cli/azure/deployment/group#az_deployment_group_show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Pour obtenir l’ID de corrélation, utilisez :

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Pour répertorier les déploiements d’un groupe de ressources, utilisez l’opération suivante. Pour obtenir le numéro de version le plus récent de l’API à utiliser dans la requête, consultez [Déploiements – Liste par groupe de ressources](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Pour obtenir un déploiement spécifique, utilisez l’opération suivante. Pour obtenir le numéro de version le plus récent de l’API à utiliser dans la requête, consultez [Déploiements – Récupérer](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

La réponse inclut l’ID de corrélation.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "11111111-1111-1111-1111-111111111111",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Récupérer des opérations de déploiement et un message d’erreur

Chaque déploiement peut comprendre plusieurs opérations. Pour plus d’informations sur un déploiement, consultez les opérations de déploiement. En cas d’échec d’un déploiement, les opérations de déploiement incluent un message d’erreur.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le résumé d’un déploiement, sélectionnez **Détails des opérations**.

   :::image type="content" source="media/deployment-history/get-operation-details.png" alt-text="Capture d’écran de l’échec du déploiement qui met en évidence le lien pour accéder aux détails de l’opération.":::

1. Vous voyez les détails de cette étape du déploiement. Lorsqu’une erreur se produit, les détails incluent le message d’erreur.

   :::image type="content" source="media/deployment-history/see-operation-details.png" alt-text="Capture d’écran des détails de l’opération du déploiement ayant échoué.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour afficher les opérations de déploiement relatives au déploiement vers un groupe de ressources, utilisez la commande [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation).

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Pour afficher les opérations ayant échoué, filtrez les opérations à l’aide de l’état **Échec**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }
```

Pour obtenir le message d’état d’opérations ayant échoué, utilisez la commande suivante :

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }).StatusMessage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour afficher les opérations de déploiement relatives au déploiement vers un groupe de ressources, utilisez la commande [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list). Vous devez disposer d’Azure CLI 2.6.0 ou version ultérieure.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Pour afficher les opérations ayant échoué, filtrez les opérations à l’aide de l’état **Échec**.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed']"
```

Pour obtenir le message d’état d’opérations ayant échoué, utilisez la commande suivante :

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Pour récupérer des opérations de déploiement, utilisez l’opération suivante. Pour obtenir le numéro de version le plus récent de l’API à utiliser dans la requête, consultez [Opérations de déploiement – Liste](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

La réponse comprend un message d’erreur.

```json
{
  "value": [
    {
      "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeployment/operations/1234567890ABCDEF",
      "operationId": "1234567890ABCDEF",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "11111111-1111-1111-1111-111111111111",
        "serviceRequestId": "11111111-1111-1111-1111-111111111111",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storage",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storage"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir de l’aide sur la résolution d’erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes de déploiement Azure](common-deployment-errors.md).
- Pour en savoir plus sur la gestion des déploiements dans l’historique, consultez [Suppressions automatiques de l’historique de déploiement](deployment-history-deletions.md).
- Pour afficher un aperçu des modifications qu’apportera un modèle avant de le déployer, consultez [Opération de simulation de déploiement de modèle ARM](deploy-what-if.md).
