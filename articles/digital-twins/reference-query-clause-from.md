---
title: Informations de référence sur le langage de requête Azure Digital Twins – clause FROM
titleSuffix: Azure Digital Twins
description: Documentation de référence sur la clause FROM du langage de requête Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 3bbd9fb23d970173e486cc721e0e2573603287a8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232846"
---
# <a name="azure-digital-twins-query-language-reference-from-clause"></a>Informations de référence sur le langage de requête Azure Digital Twins : clause FROM

Ce document contient des informations de référence sur la **clause FROM** du [langage de requête Azure Digital Twins](concepts-query-language.md).

La clause FROM est la deuxième partie d’une requête. Elle spécifie la collection et toutes les jointures sur lesquelles la requête agira.

Cette clause est obligatoire pour toutes les requêtes.

## <a name="select--from-digitaltwins"></a>SELECT ... FROM DIGITALTWINS

Utilisez `FROM DIGITALTWINS` (non sensible à la casse) pour faire référence à l’ensemble de la collection de jumeaux numériques dans une instance.

Vous pouvez éventuellement ajouter un nom à la collection de jumeaux numériques en ajoutant le nom à la fin de l’instruction.

### <a name="syntax"></a>Syntaxe

De base :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsSyntax":::

Pour nommer la collection :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedSyntax":::

### <a name="examples"></a>Exemples

Voici une requête de base. La requête suivante renvoie tous les jumeaux numériques de l’instance. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsExample":::

Voici une requête avec une collection nommée. La requête suivante attribue un nom `T` à la collection et renvoie toujours tous les jumeaux numériques de l’instance.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedExample":::

## <a name="select--from-relationships"></a>SELECT ... FROM RELATIONSHIPS

Utilisez `FROM RELATIONSHIPS` (non sensible à la casse) pour faire référence à l’ensemble de la collection de relations dans une instance.

Vous pouvez éventuellement ajouter un nom à la collection de relations en ajoutant le nom à la fin de l’instruction.

>[!NOTE]
> Cette fonctionnalité ne peut pas être combinée avec `JOIN`.

### <a name="syntax"></a>Syntaxe

De base :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsSyntax":::

Pour nommer la collection :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsNamedSyntax":::

### <a name="examples"></a>Exemples

Voici une requête qui retourne toutes les relations de l’instance. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsExample":::

Voici une requête qui retourne toutes les relations provenant des jumeaux `A`, `B`, `C` ou `D`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsFilteredExample":::

## <a name="using-from-and-join-together"></a>Utilisation conjointe de FROM et JOIN

La clause `FROM` peut être combinée avec la clause `JOIN` pour exprimer des traversées entre entités dans le graphique Azure Digital Twins.

Pour plus d’informations sur la clause `JOIN` et les requêtes de traversées du graphique, consultez [Informations de référence sur le langage de requête Azure Digital Twins : clause JOIN](reference-query-clause-join.md).

## <a name="limitations"></a>Limites

Les limites suivantes s’appliquent aux requêtes utilisant `FROM`.
* [Aucune sous-requête](#no-subqueries)
* [Choisir FROM RELATIONSHIPS ou JOIN](#choose-from-relationships-or-join)

Pour plus d’informations, consultez les sections suivantes.

### <a name="no-subqueries"></a>Aucune sous-requête

Aucune sous-requête n’est prise en charge dans l’instruction `FROM`.

#### <a name="example-negative"></a>Exemple (négatif)

La requête suivante montre un exemple de ce qui **ne peut pas** être fait selon cette limitation.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromNegativeExample":::

### <a name="choose-from-relationships-or-join"></a>Choisir FROM RELATIONSHIPS ou JOIN

La fonctionnalité `FROM RELATIONSHIPS` ne peut pas être combinée avec `JOIN`. Vous devez choisir celle de ces options qui convient le mieux aux informations que vous souhaitez sélectionner.


