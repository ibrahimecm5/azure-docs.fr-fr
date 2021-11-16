---
title: Trouver les codes d’erreur
description: Décrit comment trouver les codes d’erreur pour résoudre des problèmes de ressources Azure déployées avec des modèles Azure Resource Manager (modèles ARM) ou des fichiers Bicep.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8343dd5523c57a172dda53a8ad2d758825cccc28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479015"
---
# <a name="find-error-codes"></a>Trouver les codes d’erreur

En cas d’échec d’un déploiement de ressources Azure à l’aide de modèles Azure Resource Manager (modèles ARM) ou de fichiers Bicep, un code d’erreur est reçu. Cet article explique comment rechercher des codes d’erreur pour vous permettre de résoudre le problème. Pour plus d’informations sur les codes d’erreur, consultez [Erreurs de déploiement courantes](common-deployment-errors.md).

## <a name="error-types"></a>Types d’erreurs

Il existe trois types d’erreurs que vous pouvez rencontrer :

- Des **erreurs de validation** se produisent avant le début d’un déploiement et sont provoquées par des erreurs de syntaxe. Pour identifier les erreurs de validation, utilisez [Visual Studio Code](https://code.visualstudio.com) avec la dernière [extension Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) ou l'[extension d’outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).
- Des **erreurs de validation préalable** se produisent lorsqu’un déploiement débute, mais que les ressources ne sont pas déployées. Par exemple, une valeur de paramètre incorrecte ou un nom de ressource non valide.
- Des **Erreurs de déploiement** se produisent pendant le déploiement et la résolution des problèmes est nécessaire pour déterminer la cause.

Les codes d’erreur de validation et de validation préalable sont signalés dans le journal d’activité d’un groupe de ressources et dans le [journal d’activité](../../azure-monitor/essentials/activity-log.md) de l’abonnement. Une exception est la validation de la syntaxe Bicep qui s’affiche uniquement dans la sortie de l’éditeur ou de la commande de déploiement. Les codes d’erreur de déploiement sont affichés dans l’historique de déploiement et le journal d’activité d’un groupe de ressources.

## <a name="validation-errors"></a>Erreurs de validation

Les modèles sont validés pendant le processus de déploiement et les codes d’erreur s’affichent. Avant d’exécuter un déploiement, vous pouvez exécuter des tests de validation avec Azure PowerShell ou Azure CLI.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Un modèle ARM peut être déployé à partir du portail. Si le modèle contient des erreurs de syntaxe, une erreur de validation s’affiche lorsque vous essayez d’exécuter le déploiement. Pour plus d’informations sur les déploiements de portail, consultez [déployer des ressources à partir d’un modèle personnalisé](../templates/deploy-portal.md#deploy-resources-from-custom-template).

L’exemple suivant tente de déployer un compte de stockage et une erreur de validation se produit.

:::image type="content" source="media/find-error-code/validation-error.png" alt-text="Capture d’écran d’une erreur de validation de Portail Azure.":::

Sélectionnez le message pour obtenir plus d’informations. Le modèle contient une erreur de syntaxe avec le code d’erreur `InvalidTemplate`. Le **Résumé** indique qu’il manque une parenthèse fermante dans une expression.

:::image type="content" source="media/find-error-code/validation-details.png" alt-text="Capture d’écran d’un message d’erreur de validation qui indique une erreur de syntaxe.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour valider un modèle ARM avant le déploiement, exécutez [Test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). La même erreur s’affiche lorsque vous exécutez un déploiement.

```azurepowershell
Test-AzResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json
```

La sortie affiche des codes d’erreur tels que `InvalidTemplateDeployment` ou `AccountNameInvalid` que vous pouvez utiliser pour résoudre des problèmes et corriger le modèle.

Pour un fichier Bicep, la sortie d’un problème de validation de syntaxe indique une erreur de paramètre.

```Output
Test-AzResourceGroupDeployment: Cannot retrieve the dynamic parameters for the cmdlet.
Cannot find path '/tmp/11111111-1111-1111-1111-111111111111/main.json' because it does not exist.
```

Pour obtenir plus d’informations sur la résolution des problèmes, utilisez la commande Bicep [Build](../bicep/bicep-cli.md). La sortie affiche le numéro de ligne et de colonne de chaque erreur entre parenthèses, et le message d’erreur.

```bicep
bicep build main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

D’autres applets de commande PowerShell sont disponibles pour valider les modèles de déploiement :

- [Test-AzDeployment](/powershell/module/az.resources/test-azdeployment) pour des déploiements au niveau de l’abonnement.
- [Test-AzManagementGroupDeployment](/powershell/module/az.resources/test-azmanagementgroupdeployment)
- [Test-AzTenantDeployment](/powershell/module/az.resources/test-aztenantdeployment)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour valider un modèle ARM avant le déploiement, exécutez [az deployment group validate](/cli/azure/deployment/group#az_deployment_group_validate). La même erreur s’affiche lorsque vous exécutez un déploiement.

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file azuredeploy.json
```

La sortie affiche des codes d’erreur tels que `InvalidTemplateDeployment` ou `AccountNameInvalid` que vous pouvez utiliser pour résoudre des problèmes et corriger le modèle.

Pour un fichier Bicep, la sortie affiche le numéro de ligne et de colonne de chaque erreur entre parenthèses, et le message d’erreur.

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

D’autres commandes Azure CLI sont disponibles pour valider les modèles de déploiement :

- [az deployment sub validate](/cli/azure/deployment/sub#az_deployment_sub_validate)
- [az deployment mg validate](/cli/azure/deployment/mg#az_deployment_mg_validate)
- [az deployment tenant validate](/cli/azure/deployment/tenant#az_deployment_tenant_validate)

---

## <a name="deployment-errors"></a>Erreurs de déploiement

Plusieurs opérations sont traitées pour déployer une ressource Azure. Des erreurs de déploiement se produisent quand une opération réussit la validation, mais qu’elle échoue pendant le déploiement. Vous pouvez afficher les messages relatifs à chaque opération de déploiement et à chaque déploiement pour un groupe de ressources.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour afficher les messages relatifs aux opérations d’un déploiement, utilisez le **Journal d’activité** du groupe de ressources :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Groupes de ressources**, puis sélectionnez le nom du groupe de ressources du déploiement.
1. Sélectionnez **Journal d’activité**.
1. Utilisez les filtres pour rechercher le journal des erreurs d’une opération.

    :::image type="content" source="./media/find-error-code/activity-log.png" alt-text="Capture d’écran du journal d’activité du groupe de ressources mettant en évidence un échec de déploiement.":::

1. Sélectionnez le journal des erreurs pour afficher les détails de l’opération.

    :::image type="content" source="./media/find-error-code/activity-log-details.png" alt-text="Capture d’écran des détails du journal d’activité indiquant le message d’erreur d’un échec de déploiement.":::

Pour afficher le résultat d’un déploiement :

1. Accédez au groupe de ressources.
1. Sélectionnez **Paramètres** > **Déploiements**.
1. Sélectionnez **Détails de l’erreur** pour le déploiement.

    :::image type="content" source="media/find-error-code/deployment-error-details.png" alt-text="Capture d’écran du lien d’un groupe de ressources vers les détails de l’erreur pour un déploiement qui a échoué.":::

1. Le message d'erreur et le code d’erreur `NoRegisteredProviderFound` s’affichent.

    :::image type="content" source="media/find-error-code/deployment-error-summary.png" alt-text="Capture d’écran d’un message qui affiche les détails de l’erreur de déploiement.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour afficher les messages d’opération d’un déploiement avec PowerShell, utilisez [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation).

Pour afficher toutes les opérations d’un déploiement :

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

Pour spécifier un type de propriété spécifique :

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusCode
```

Pour obtenir le résultat d’un déploiement, utilisez [Get-AzResourceGroupDeployment](/powershell/module/az.resources/get-azresourcegroupdeployment).

```azurepowershell
Get-AzResourceGroupDeployment `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour afficher les messages d’opération d’un déploiement avec Azure CLI, utilisez [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list).

Pour afficher toutes les opérations d’un déploiement :

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties"
```

Pour spécifier un type de propriété spécifique :

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties.statusCode"
```

Pour obtenir le résultat d’un déploiement, utilisez [az deployment group show](/cli/azure/deployment/group#az_deployment_group_show).

```azurecli
az deployment group show \
  --resource-group examplegroup \
  --name exampledeployment
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Erreurs de déploiement courantes](common-deployment-errors.md)
- [Activer l’enregistrement du débogage](enable-debug-logging.md)
- [Créer un modèle de résolution des problèmes](create-troubleshooting-template.md)
