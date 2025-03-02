---
title: 'Modèle : Grouper des définitions de stratégie avec des initiatives'
description: Ce modèle Azure Policy fournit un exemple de regroupement des définitions de stratégie dans une initiative.
ms.date: 08/17/2021
ms.topic: sample
ms.openlocfilehash: 0311da9da70186cf8a244b9bcb8b4b68d3e6ccdc
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324171"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Modèle Azure Policy : Grouper des définitions de stratégie

Une initiative est un groupe de définitions de stratégie. En groupant des définitions de stratégie associées dans un objet unique, vous pouvez créer une seule affectation au lieu de plusieurs.

## <a name="sample-initiative-definition"></a>Exemple de définition d’initiative

Cette initiative déploie deux définitions de stratégie, chacune prenant les paramètres **tagName** et **tagValue**. L’initiative elle-même a deux paramètres : **costCenterValue** et **productNameValue**.
Ces paramètres d’initiative sont fournis à chacune des définitions de stratégie groupées. Cette conception optimise la réutilisation des définitions de stratégie existantes tout en limitant le nombre d’affectations créées pour les implémenter en fonction des besoins.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Explication

#### <a name="initiative-parameters"></a>Paramètres d’initiative

Une initiative peut définir ses propres paramètres, qui sont ensuite transmis aux définitions de stratégie groupées.
Dans cet exemple, **costCenterValue** et **productNameValue** sont définis en tant que paramètres d’initiative. Les valeurs sont fournies quand l’initiative est affectée.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Définitions de stratégie incluses

Chaque définition de stratégie incluse doit fournir le **policyDefinitionId** et un tableau **parameters** si la définition de stratégie accepte des paramètres. Dans l’extrait de code suivant, la définition de stratégie incluse accepte deux paramètres : **tagName** et **tagValue**. **tagName** est défini avec un littéral, mais **tagValue** utilise le paramètre **costCenterValue** défini par l’initiative. Ce relais de valeurs améliore la réutilisation.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les autres [modèles et définitions intégrées](./index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
