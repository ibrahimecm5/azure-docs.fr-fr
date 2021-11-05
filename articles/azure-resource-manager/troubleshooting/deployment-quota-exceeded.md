---
title: Dépassement du quota de déploiement
description: Explique comment résoudre l'erreur liée à la présence de plus de 800 déploiements dans l'historique du groupe de ressources.
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 06383a47f2fc7603bdc112d1fe0cd0ca53930bd6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096804"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Résoudre l'erreur liée à un nombre de déploiements supérieur à 800

L'historique de déploiement de chaque groupe de ressources est limité à 800 déploiements. Cet article décrit l'erreur que vous recevez lorsqu'un déploiement échoue pour cause de dépassement du quota des 800 déploiements autorisés. Pour résoudre cette erreur, supprimez des déploiements de l'historique du groupe de ressources. La suppression d'un déploiement de l'historique n'a aucun impact sur les ressources déployées.

Azure Resource Manager supprime automatiquement les déploiements de votre historique lorsque vous vous approchez de la limite. Vous pouvez toujours voir cette erreur pour l’une des raisons suivantes :

1. Vous disposez d’un verrou CanNotDelete sur le groupe de ressources qui empêche les suppressions de l’historique de déploiement.
1. Vous avez refusé les suppressions automatiques.
1. Un grand nombre de déploiements s’exécutent simultanément et les suppressions automatiques ne sont pas assez rapides pour réduire le nombre total.

Pour plus d’informations sur la suppression du verrou ou l’activation des suppressions automatiques, consultez [Suppressions automatiques de l’historique de déploiement](../templates/deployment-history-deletions.md).

Cet article explique comment supprimer manuellement des déploiements de l’historique.

## <a name="symptom"></a>Symptôme

Pendant un déploiement, vous recevez une erreur indiquant que celui-ci va entraîner un dépassement du quota de 800 déploiements.

## <a name="solution"></a>Solution

### <a name="azure-cli"></a>Azure CLI

Utilisez la commande [az deployment group delete](/cli/azure/group/deployment) pour supprimer des déploiements de l’historique.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Pour supprimer tous les déploiements datant de plus de cinq jours, utilisez :

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Pour connaître le nombre de déploiements actuellement contenu dans l'historique, utilisez la commande suivante :

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Utilisez la commande [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) pour supprimer des déploiements de l'historique.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Pour supprimer tous les déploiements datant de plus de cinq jours, utilisez :

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Pour connaître le nombre de déploiements actuellement contenu dans l'historique, utilisez la commande suivante :

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Solutions tierces

Les solutions externes suivantes s'appliquent à des scénarios spécifiques :

* [Solutions Azure Logic Apps et PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzureDevOpsExtensionCleanRG](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
