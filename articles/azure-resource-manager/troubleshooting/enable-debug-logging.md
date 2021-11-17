---
title: Activer l’enregistrement du débogage
description: Décrit comment activer la journalisation du débogage pour résoudre des problèmes de ressources Azure déployées avec des modèles Azure Resource Manager (modèles ARM) ou des fichiers Bicep.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fcd2cbdf052f934bb797b3f1dab148d951d09167
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989646"
---
# <a name="enable-debug-logging"></a>Activer l’enregistrement du débogage

Pour corriger une erreur de déploiement, il est utile de recueillir des informations supplémentaires. Utilisez Azure PowerShell pour activer la journalisation du débogage. Vous pouvez obtenir des données sur la demande et la réponse d’un déploiement de connaître la cause du problème. La journalisation du débogage fonctionne avec les modèles Azure Resource Manager (modèles ARM) et les fichiers Bicep.

## <a name="get-debug-information"></a>Obtenir des informations de débogage

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez la commande [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) pour définir le paramètre `DeploymentDebugLogLevel` sur `All`, `ResponseContent` ou `RequestContent`. Lorsque la journalisation du débogage est activée, un avertissement s’affiche, indiquant que des secrets tels que des mots de passe ou `listKeys` peuvent être consignés par des commandes telles que [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation).

```azurepowershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json `
  -DeploymentDebugLogLevel All
```

La sortie affiche la journalisation du débogage :

```Output
DeploymentDebugLogLevel : RequestContent, ResponseContent
```

Pour afficher toutes les propriétés des opérations de déploiement :

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

Vous pouvez spécifier une propriété, comme `StatusMessage` ou `StatusCode` pour filtrer la sortie.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusMessage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous ne pouvez pas activer la journalisation du débogage avec Azure CLI, mais vous pouvez récupérer les données de journalisation du débogage.

Récupérez les opérations de déploiement à l’aide de la commande [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list) :

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Récupérez le contenu de la demande à l’aide de la commande suivante :

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.request
```

Récupérez le contenu de la réponse à l’aide de la commande suivante :

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.response
```

---

## <a name="nested-template"></a>Modèle imbriqué

Pour journaliser les informations de débogage pour un modèle ARM [imbriqué](../templates/linked-templates.md#nested-template), utilisez l’élément `debugSetting` [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments).

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

Bicep utilise des [modules](../bicep/modules.md) au lieu de `Microsoft.Resources/deployments`. Avec des modules, vous pouvez réutiliser votre code pour déployer un fichier Bicep à partir d’un autre.

## <a name="next-steps"></a>Étapes suivantes

- [Erreurs de déploiement courantes](common-deployment-errors.md)
- [Trouver les codes d’erreur](find-error-code.md)
- [Créer un modèle de résolution des problèmes](create-troubleshooting-template.md)
