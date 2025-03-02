---
title: Déterminer les causes de non-conformité
description: De nombreuses raisons peuvent expliquer une ressource non conforme. Découvrez comment identifier l'origine d'une non-conformité.
ms.date: 09/01/2021
ms.topic: how-to
ms.openlocfilehash: 3c4076673adfe3253a418cc648592e72a8979b5a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433802"
---
# <a name="determine-causes-of-non-compliance"></a>Déterminer les causes de non-conformité

Lorsqu'une ressource Azure est jugée non conforme par rapport à une règle de stratégie, il est important d'identifier la partie de la règle concernée. Il est également important d'identifier la modification qui a transformé une ressource conforme en ressource non conforme. Pour ce faire, il existe deux moyens :

- [Détails de conformité](#compliance-details)
- [Historique des changements (préversion)](#change-history)

## <a name="compliance-details"></a>Détails de conformité

Lorsqu’une ressource est non conforme, les détails de conformité de cette ressource sont disponibles sur la page **Conformité à la stratégie**. Le volet relatif aux détails de conformité comprend les informations suivantes :

- Détails de la ressource tels que son nom, son type, son emplacement et son ID
- État de conformité et timestamp de la dernière évaluation ayant trait à l'attribution de stratégie actuelle
- Liste des _motifs_ de non-conformité de la ressource

> [!IMPORTANT]
> Les détails de conformité d'une ressource _non conforme_ affichant la valeur actuelle des propriétés qui s'y rapportent, l’utilisateur doit pouvoir utiliser une opération en **lecture** pour le **type** de ressource. Par exemple, si la ressource _non conforme_ est **Microsoft.Compute/virtualmachines**, l’utilisateur doit pouvoir utiliser l'opération **Microsoft.Compute/virtualMachines/read**. Dans le cas contraire, une erreur d'accès s'affiche.

Pour afficher les détails de conformité, procédez comme suit :

1. Lancez le service Azure Policy dans le portail Azure en sélectionnant **Tous les services**, puis en recherchant et en sélectionnant **Policy**.

1. Sur la page **Vue d'ensemble** ou **Conformité**, sélectionnez une stratégie affichant un **état de conformité**_Non conforme_.

1. Sous l’onglet **Conformité des ressources** de la page **Conformité à la stratégie**, sélectionnez et maintenez (ou cliquez avec le bouton droit) ou sélectionnez les points de suspension d’une ressource affichant un **état de conformité**_non conforme_. Sélectionnez ensuite **Afficher les détails de la conformité**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Capture d’écran du lien « Afficher les détails de la conformité » sous l’onglet Conformité des ressources." border="false":::

1. Le volet **Détails de conformité** affiche des informations issues de la dernière évaluation de la ressource ayant trait à l'attribution de stratégie actuelle. Dans cet exemple, le champ **Microsoft.Sql/servers/version** indique _12.0_ alors que la définition de la stratégie attendait _14.0_. Si la ressource est non conforme pour plusieurs raisons, ces différentes raisons sont répertoriées dans ce volet.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Capture d’écran du volet Détails de conformité et des raisons de non-conformité pour lesquelles la valeur actuelle est douze et la valeur cible quatorze." border="false":::

   Pour une définition de stratégie **auditIfNotExists** ou **deployIfNotExists**, les détails incluent la propriété **details.type** et autres propriétés facultatives. Pour une liste, consultez [Propriétés auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) et [Propriétés deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Dernière ressource évaluée** correspond à une ressource liée dans la section **Détails** de la définition.

   Exemple de définition partielle **deployIfNotExists** :

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Capture d’écran du volet Détails de conformité pour ifNotExists, comprenant le nombre de ressources évaluées." border="false":::

> [!NOTE]
> Pour protéger les données, lorsqu’une valeur de propriété correspond à un _secret_, la valeur actuelle est remplacée par des astérisques.

Ces détails expliquent les raisons pour lesquelles une ressource est actuellement non conforme, mais n'indiquent pas le moment où la modification apportée à la ressource l'a rendue non conforme. Pour plus d’informations, consultez [Historique des modifications (préversion)](#change-history) ci-dessous.

### <a name="compliance-reasons"></a>Motifs de conformité

Les [modes gestionnaire des ressources](../concepts/definition-structure.md#resource-manager-modes) et les [modes fournisseur de ressources](../concepts/definition-structure.md#resource-provider-modes) ont chacun des _raisons_ de non-conformité.

#### <a name="general-resource-manager-mode-compliance-reasons"></a>Raisons générales de conformité du mode gestionnaire des ressources

Le tableau suivant mappe chaque _raison_ du [mode gestionnaire des ressources](../concepts/definition-structure.md#resource-manager-modes) à la [condition](../concepts/definition-structure.md#conditions) responsable dans la définition de stratégie :

|Motif |Condition |
|-|-|
|La valeur actuelle doit contenir la valeur cible en tant que clé. |containsKey ou **not** notContainsKey |
|La valeur actuelle doit contenir la valeur cible. |contains ou **not** notContains |
|La valeur actuelle doit être égale à la valeur cible. |equals ou **not** notEquals |
|La valeur actuelle doit être inférieure à la valeur cible. |less ou **not** greaterOrEquals |
|La valeur actuelle doit être supérieure ou égale à la valeur cible. |greaterOrEquals ou **not** less |
|La valeur actuelle doit être supérieure à la valeur cible. |greater ou **not** lessOrEquals |
|La valeur actuelle doit être inférieure ou égale à la valeur cible. |lessOrEquals ou **not** greater |
|La valeur actuelle doit exister. |exists |
|La valeur actuelle doit être dans la valeur cible. |in ou **not** notIn |
|La valeur actuelle doit être identique à la valeur cible. |like ou **not** notLike |
|La valeur actuelle doit correspondre à la valeur cible (sensibilité à la casse). |match ou **not** notMatch |
|La valeur actuelle doit correspondre à la valeur cible (non-sensibilité à la casse). |matchInsensitively ou **not** notMatchInsensitively |
|La valeur actuelle ne doit pas contenir la valeur cible en tant que clé. |notContainsKey ou **not** containsKey|
|La valeur actuelle ne doit pas contenir la valeur cible. |notContains ou **not** contains |
|La valeur actuelle ne doit pas être égale à la valeur cible. |notEquals ou **not** equals |
|La valeur actuelle ne doit pas exister. |**not** exists  |
|La valeur actuelle ne doit pas être dans la valeur cible. |notIn ou **not** in |
|La valeur actuelle ne doit pas être identique à la valeur cible. |notLike ou **not** like |
|La valeur actuelle ne doit pas correspondre à la valeur cible (sensibilité à la casse). |notMatch ou **not** match |
|La valeur actuelle ne doit pas correspondre à la valeur cible (non-sensibilité à la casse). |notMatchInsensitively ou **not** matchInsensitively |
|Aucune ressource associée ne correspond aux détails de l'effet dans la définition de stratégie. |Aucune ressource du type défini dans **then.details.type** n'est associée à la ressource définie dans la portion **if**. |

#### <a name="aks-resource-provider-mode-compliance-reasons"></a>Raisons de conformité du mode fournisseur de ressources AKS

Le tableau suivant mappe chaque _raison_ du `Microsoft.Kubernetes.Data`
[mode fournisseur de ressources](../concepts/definition-structure.md#resource-provider-modes) à l’état responsable du [modèle de contrainte](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates) dans la définition de stratégie :

|Motif |Description du motif du modèle de contrainte |
|-|-|
|Constraint/TemplateCreateFailed |La ressource n’a pas pu être créée pour une définition de stratégie avec une contrainte/un modèle qui ne correspond pas à une contrainte/un modèle existant sur le cluster par nom des métadonnées de ressource. |
|Constraint/TemplateUpdateFailed |La mise à jour de la contrainte/du modèle a échoué pour une définition de stratégie avec une contrainte/un modèle qui correspond à un modèle/contrainte existant sur le cluster par le nom des métadonnées de ressource. |
|Constraint/TemplateInstallFailed |La génération de la contrainte/du modèle a échoué, et ils n’ont pas pu être installés sur le cluster pour une opération de création ou de mise à jour. |
|ConstraintTemplateConflicts |Le modèle est en conflit avec une ou plusieurs définitions de stratégie utilisant le même nom de modèle avec une source différente. |
|ConstraintStatusStale |Il existe un état « Audit » existant, mais Gatekeeper n’a pas effectué d’audit au cours de la dernière heure. |
|ConstraintNotProcessed |Il n’y a pas d’état, et Gatekeeper n’a pas effectué d’audit au cours de la dernière heure. |
|InvalidConstraint/Template |Le serveur API a rejeté la ressource en raison d’un YAML incorrect. Cette raison peut également être due à une incompatibilité de type de paramètre (exemple : chaîne fournie pour un entier)

> [!NOTE]
> Pour les attributions de stratégie et les modèles de contrainte existants sur le cluster, si la contrainte/le modèle échoue, le cluster est protégé en conservant la contrainte/le modèle existant. Le cluster signale comme non conforme jusqu’à ce que la défaillance soit résolue sur l’attribution de stratégie ou les réparations spontanées du module complémentaire. Pour plus d’informations sur la gestion des conflits, consultez [Conflits de modèle de contrainte](../concepts/policy-for-kubernetes.md#constraint-template-conflicts).

## <a name="component-details-for-resource-provider-modes"></a>Détails du composant pour les modes du fournisseur de ressources

Pour les affectations avec un [mode Fournisseur de ressources](../concepts/definition-structure.md#resource-provider-modes), sélectionnez la ressource _non conforme_ pour ouvrir une vue plus détaillée. Sous l’onglet **Conformité des composants**, vous pouvez obtenir des informations supplémentaires spécifiques au mode Fournisseur de ressources sur la stratégie attribuée, qui indique le _composant_ **non conforme** et l’**ID du composant**.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Capture d’écran de l’onglet Conformité des composants et des détails de conformité pour une affectation de mode de fournisseur de ressources." border="false":::

## <a name="compliance-details-for-guest-configuration"></a>Détails de conformité pour la configuration invité

Pour les définitions de stratégie de la catégorie _Configuration d’invité_, plusieurs paramètres peuvent être évalués dans la machine virtuelle et vous devez examiner les détails par paramètre. Par exemple, si vous effectuez un audit pour obtenir la liste des paramètres de sécurité et qu’une seule d’entre-elles a l’état _Non conforme_, vous devez connaître les paramètres de sécurité spécifiques qui ne sont pas conformes et la raison à cela.

Vous ne serez peut-être pas autorisé à vous connecter directement à la machine virtuelle, mais devrez indiquer pourquoi elle est _Non conforme_.

### <a name="azure-portal"></a>Portail Azure

Commencez par suivre les étapes de la section ci-dessus pour afficher les détails de conformité à la stratégie.

Dans le volet Détails de conformité, sélectionnez le lien **Dernière ressource évaluée**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Capture d’écran des détails de conformité de la définition auditIfNotExists." border="false":::

La page **Affectation d'invité** affiche les détails de conformité disponibles. Chaque ligne de la vue représente une évaluation effectuée au sein de la machine. Dans la colonne **Raison**, une phrase s’affiche pour décrire la raison pour laquelle l’attribution d’invité est _Non conforme_. Par exemple, si vous auditez les stratégies de mot de passe, la colonne **Raison** affiche du texte, dont la valeur actuelle de chaque paramètre.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Capture d’écran des détails de conformité de l’affectation d’invité." border="false":::

### <a name="view-configuration-assignment-details-at-scale"></a>Afficher les détails d’attribution de configuration à grande échelle

Vous pouvez utiliser la fonctionnalité de configuration d’invité en dehors des affectations Azure Policy.
Par exemple, [Azure Automanage](../../../automanage/automanage-virtual-machines.md) crée des affectations de configuration d’invité, ou vous pouvez [affecter des configurations quand vous déployez des machines](guest-configuration-create-assignment.md).

Pour afficher toutes les affectations de configuration d’invité dans votre locataire, dans le portail Azure, ouvrez la page **Affectations d’invités**. Pour afficher les informations de conformité détaillées, sélectionnez chaque affectation en utilisant le lien figurant dans la colonne « Nom ».

:::image type="content" source="../media/determine-non-compliance/guest-config-assignment-view.png" alt-text="Capture d’écran de la page Affectation d’invité." border="true":::

## <a name="change-history-preview"></a><a name="change-history"></a>Historique des modifications (préversion)

Dans le cadre d’une nouvelle **préversion publique**, l'historique des modifications des 14 derniers jours est disponible pour toutes les ressources Azure prenant en charge la [suppression du mode](../../../azure-resource-manager/templates/deployment-complete-mode-deletion.md). L'historique des modifications indique quand une modification a été détectée et fournit un _différentiel visuel_ pour chaque modification. Une détection de modification est déclenchée lorsque les propriétés Azure Resource Manager sont ajoutées, supprimées ou modifiées.

1. Lancez le service Azure Policy dans le portail Azure en sélectionnant **Tous les services**, puis en recherchant et en sélectionnant **Policy**.

1. Sur la page **Vue d'ensemble** ou **Conformité**, sélectionnez une stratégie dans un **état de conformité**.

1. Sous l'onglet **Conformité des ressources** de la page **Conformité à la stratégie**, sélectionnez une ressource.

1. Sélectionnez l'onglet **Historique des modifications (préversion)** de la page **Conformité des ressources**. La liste des modifications détectées, le cas échéant, s'affiche.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Capture d’écran de l’onglet Historique des changements et des heures de modification détectées dans la page Conformité des ressources." border="false":::

1. Sélectionnez une des modifications détectées. Le _différentiel visuel_ de la ressource est présenté sur la page **Historique des modifications**.

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Capture d’écran du différentiel visuel des états antérieur et postérieur des propriétés dans la page Historique des changements." border="false":::

Le _différentiel visuel_ aide à identifier les modifications apportées à une ressource. Les modifications détectées peuvent ne pas être liées à l’état de conformité actuel de la ressource.

Les données de l'historique des modifications sont fournies par [Azure Resource Graph](../../resource-graph/overview.md). Pour interroger ces informations en dehors du portail Azure, consultez [Obtenir les modifications des ressources](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Étapes suivantes

- Consultez des exemples à la page [Exemples Azure Policy](../samples/index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
- Découvrez comment [créer des stratégies par programmation](programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](get-compliance-data.md).
- Découvrez comment [corriger des ressources non conformes](remediate-resources.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).