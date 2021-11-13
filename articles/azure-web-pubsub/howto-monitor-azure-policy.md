---
title: Conformité du service Azure Web PubSub avec Azure Policy
description: Attribuez des stratégies intégrées dans Azure Policy pour auditer la conformité de vos ressources de service Azure Web PubSub.
author: JialinXin
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 10/25/2021
ms.author: jixin
ms.openlocfilehash: a755b0bb9e01dff95c83bb2caf8d7773bb2d46d4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270933"
---
# <a name="audit-compliance-of-azure-web-pubsub-service-resources-using-azure-policy"></a>Auditer la conformité des ressources du service Azure Web PubSub à l’aide d’Azure Policy

[Azure Policy](../governance/policy/overview.md) est un service d’Azure que vous utilisez pour créer, affecter et gérer des stratégies. Ces stratégies appliquent différentes règles et effets sur vos ressources, qui restent donc conformes aux normes et aux contrats de niveau de service de l’entreprise.

Cet article présente les stratégies intégrées (préversion) pour le service Azure Web PubSub. Utilisez ces stratégies pour auditer la conformité des ressources Web PubSub nouvelles et existantes.

L’utilisation d’Azure Policy est gratuite.

## <a name="built-in-policy-definitions"></a>Définitions de stratégie intégrées

Les définitions de stratégies intégrées suivantes sont propres au service Azure Web PubSub :

[!INCLUDE [azure-policy-reference-policies-web-pubsub](../../includes/policy/reference/bycat/policies-web-pubsub.md)]

## <a name="assign-policy-definitions"></a>Attribuer des définitions de stratégie

* Attribuez des définitions de stratégie à l’aide du [portail Azure](../governance/policy/assign-policy-portal.md), d’[Azure CLI](../governance/policy/assign-policy-azurecli.md), d’un [modèle Resource Manager](../governance/policy/assign-policy-template.md) ou des Kits de développement logiciel (SDK) Azure Policy.
* Étendez une attribution de stratégie à un groupe de ressources, à un abonnement ou à un [groupe d’administration Azure](../governance/management-groups/overview.md). Les attributions de stratégie Web PubSub s’appliquent aux ressources Web PubSub nouvelles et existantes au sein de l’étendue.
* Activez ou désactivez l’[application de la stratégie](../governance/policy/concepts/assignment-structure.md#enforcement-mode) à tout moment.

> [!NOTE]
> Une fois que vous avez attribué ou mis à jour une stratégie, l’application de l’attribution aux ressources de l’étendue définie prend un certain temps. Consultez les informations relatives aux [déclencheurs d’évaluation de la stratégie](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Vérifier la conformité de la stratégie

Accédez aux informations de conformité générées par vos affectations de stratégie à l’aide du Portail Azure, des outils en ligne de commande Azure ou des Kits de développement logiciel (SDK) Azure Policy. Pour plus d’informations, consultez [Obtenir les données de conformité des ressources Azure](../governance/policy/how-to/get-compliance-data.md).

De nombreuses raisons peuvent expliquer une ressource non conforme. Pour en déterminer la raison ou pour trouver la modification en cause, consultez [Déterminer une non-conformité](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Conformité de la stratégie dans le portail :

1. Sélectionnez **Tous les services** et recherchez **Stratégie**.
1. Sélectionnez **Conformité**.
1. Utilisez les filtres pour limiter les états de conformité ou pour rechercher des stratégies.
   
    [ ![Policy compliance in portal](./media/howto-monitor-azure-policy/azure-policy-compliance.png) ](./media/howto-monitor-azure-policy/azure-policy-compliance.png#lightbox)
2. Sélectionnez une stratégie pour passer en revue l’ensemble des détails et des événements relatifs à la conformité. Si vous le souhaitez, sélectionnez une ressource Web PubSub spécifique pour la conformité des ressources.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformité de la stratégie dans Azure CLI

Vous pouvez également utiliser l’interface de ligne de commande Azure pour accéder aux données de conformité. Par exemple, utilisez la commande [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) dans l’interface CLI pour obtenir les ID des stratégies du service Azure Web PubSub qui sont appliquées :

```azurecli
az policy assignment list --query "[?contains(displayName,'Web PubSub')].{name:displayName, ID:id}" --output table
```

Exemple de sortie :

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure Web PubSub Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Exécutez ensuite [az policy state list](/cli/azure/policy/state#az_policy_state_list) pour retourner l’état de conformité au format JSON pour toutes les ressources sous un groupe de ressources spécifique :

```azurecli
az policy state list --g <resourceGroup>
```

Ou exécutez [az policy state list](/cli/azure/policy/state#az_policy_state_list) pour retourner l’état de conformité au format JSON d’une ressource Web PubSub spécifique :

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/WebPubSub/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [définitions](../governance/policy/concepts/definition-structure.md) et les [effets](../governance/policy/concepts/effects.md) Azure Policy

* Créer une [définition de stratégie personnalisée](../governance/policy/tutorials/create-custom-policy-definition.md)

* En savoir plus sur les [capacités de gouvernance](../governance/index.yml) dans Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/