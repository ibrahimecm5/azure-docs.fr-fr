---
title: Dépassement du quota de déploiement
description: Explique comment résoudre l'erreur liée à la présence de plus de 800 déploiements dans l'historique du groupe de ressources.
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 64845ad3aed7dcccb7623a84552459ac9de78945
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489848"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Résoudre l'erreur liée à un nombre de déploiements supérieur à 800

L'historique de déploiement de chaque groupe de ressources est limité à 800 déploiements. Cet article décrit l'erreur que vous recevez lorsqu'un déploiement échoue pour cause de dépassement du quota des 800 déploiements autorisés. Pour résoudre cette erreur, supprimez des déploiements de l'historique du groupe de ressources. La suppression d'un déploiement de l'historique n'a aucun impact sur les ressources déployées.

Azure Resource Manager supprime automatiquement les déploiements de votre historique lorsque vous vous approchez de la limite. Vous pouvez toujours voir cette erreur pour l’une des raisons suivantes :

1. Vous disposez d’un verrou [CanNotDelete](../management/lock-resources.md) sur le groupe de ressources qui empêche les suppressions de l’historique de déploiement.
1. Vous avez refusé les suppressions automatiques.
1. Un grand nombre de déploiements s’exécutent simultanément et les suppressions automatiques ne sont pas assez rapides pour réduire le nombre total.

Pour plus d’informations sur la façon de supprimer un verrou ou de choisir des suppressions automatiques, consultez [Suppressions automatiques de l’historique de déploiement](../templates/deployment-history-deletions.md).

Cet article explique comment supprimer manuellement des déploiements de l’historique.

## <a name="symptom"></a>Symptôme

Pendant un déploiement, vous recevez une erreur qui indique que celui-ci va entraîner un dépassement du quota de 800 déploiements.

## <a name="solution"></a>Solution

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az deployment group delete](/cli/azure/deployment/group#az_deployment_group_delete) pour supprimer des déploiements de l’historique.

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez la commande [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) pour supprimer des déploiements de l'historique.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Pour supprimer tous les déploiements datant de plus de cinq jours, utilisez :

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object -Property Timestamp -LT -Value ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Pour connaître le nombre de déploiements actuellement contenu dans l'historique, utilisez la commande suivante :

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

---
