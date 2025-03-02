---
title: Suppressions de l’historique de déploiement
description: Décrit la manière dont Azure Resource Manager supprime automatiquement les déploiements de l’historique de déploiement. Les déploiements sont supprimés lorsque l’historique approche de la limite des 800 déploiements.
ms.topic: conceptual
ms.date: 06/04/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: eaffae3ea5e901719969632cb1f889c8914978a0
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963349"
---
# <a name="automatic-deletions-from-deployment-history"></a>Suppressions automatiques de l’historique de déploiement

Chaque fois que vous déployez un modèle, les informations concernant le déploiement sont écrites dans l’historique de déploiement. L'historique de déploiement de chaque groupe de ressources est limité à 800 déploiements.

Azure Resource Manager supprime automatiquement les déploiements de votre historique lorsque vous vous approchez de la limite. Cette suppression automatique est un changement de comportement par rapport au passé. Auparavant, vous deviez supprimer manuellement les déploiements de l’historique des déploiements pour éviter d’obtenir une erreur. Cette modification a été implémentée le 6 août 2020.

**Les suppressions automatiques sont prises en charge pour les déploiements de groupes de ressources et d’abonnements. À l’heure actuelle, les déploiements de [groupes d’administration](deploy-to-management-group.md) et de [locataires](deploy-to-tenant.md) qui figurent dans l’historique ne sont pas supprimés automatiquement.**

> [!NOTE]
> La suppression d'un déploiement de l'historique n'a aucun impact sur les ressources déployées.

## <a name="when-deployments-are-deleted"></a>Lors de la suppression des déploiements

Les déploiements sont supprimés de votre historique lorsque vous dépassez 700 déploiements. Azure Resource Manager en supprime jusqu’à ce que l’historique descende à 600. Les déploiements les plus anciens sont toujours supprimés en premier.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.png" alt-text="Diagramme de la suppression de l’historique de déploiement.":::

> [!IMPORTANT]
> Si votre groupe de ressources est déjà à la limite de 800, le prochain déploiement échoue avec une erreur. Le processus de suppression automatique démarre immédiatement. Vous pouvez réessayer votre déploiement après une brève attente.

En plus des déploiements, vous déclenchez également des suppressions lorsque vous exécutez l’[opération de simulation](./deploy-what-if.md) ou validez un déploiement.

Lorsque vous donnez à un déploiement le même nom qu’un autre dans l’historique, vous réinitialisez sa place dans l’historique. Le déploiement passe au rang le plus récent dans l’historique. Vous pouvez également réinitialiser le rang d’un déploiement lorsque vous [restaurer ce déploiement](rollback-on-error.md) après une erreur.

## <a name="remove-locks-that-block-deletions"></a>Supprimer les verrous qui bloquent les suppressions

Si vous avez un [verrou CanNotDelete](../management/lock-resources.md) sur un groupe de ressources, les déploiements pour ce groupe de ressources ne peuvent pas être supprimés. Vous devez supprimer le verrou pour tirer parti des suppressions automatiques dans l’historique de déploiement.

Pour utiliser PowerShell afin de supprimer un verrou, exécutez les commandes suivantes :

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Pour utiliser Azure CLI afin de supprimer un verrou, exécutez les commandes suivantes :

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Autorisations requises

Les suppressions sont demandées sous l’identité de l’utilisateur qui a déployé le modèle. Pour supprimer des déploiements, l’utilisateur doit avoir accès à l’action **Microsoft.Resources/deployments/delete**. Si l’utilisateur ne dispose pas des autorisations requises, les déploiements ne sont pas supprimés de l’historique.

Si l’utilisateur actuel ne dispose pas des autorisations requises, la suppression automatique est tentée à nouveau lors du déploiement suivant.

## <a name="opt-out-of-automatic-deletions"></a>Refuser les suppressions automatiques

Vous pouvez refuser les suppressions automatiques de l’historique. **Utilisez cette option uniquement lorsque vous souhaitez gérer vous-même l’historique des déploiements.** La limite de 800 déploiements dans l’historique est toujours en vigueur. Si vous dépassez les 800 déploiements, vous recevrez une erreur et votre déploiement échouera.

Pour désactiver les suppressions automatiques, inscrivez l’indicateur de fonctionnalité `Microsoft.Resources/DisableDeploymentGrooming`. Lorsque vous enregistrez l’indicateur de fonctionnalité, vous refusez les suppressions automatiques pour l’intégralité de l’abonnement Azure. Vous ne pouvez pas refuser uniquement pour un groupe de ressources particulier. Pour réactiver les suppressions automatiques, désinscrivez l’indicateur de fonctionnalité.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour PowerShell, utilisez [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Pour afficher l’état actuel de votre abonnement, utilisez :

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Pour réactiver les suppressions automatiques, utilisez l’API REST Azure ou Azure CLI.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour Azure CLI, utilisez [az feature register](/cli/azure/feature#az_feature_register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Pour afficher l’état actuel de votre abonnement, utilisez :

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Pour réactiver les suppressions automatiques, utilisez [az Feature Unregister](/cli/azure/feature#az_feature_unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Pour l’API REST, utilisez [Fonctionnalités – Inscrire](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Pour afficher l’état actuel de votre abonnement, utilisez :

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Pour réactiver les suppressions automatiques, utilisez [fonctionnalités - annuler l’inscription](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’affichage de l’historique des déploiements, consultez [Afficher l’historique des déploiements avec Azure Resource Manager](deployment-history.md).