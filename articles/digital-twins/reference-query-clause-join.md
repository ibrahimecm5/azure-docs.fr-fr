---
title: Informations de référence sur le langage de requête Azure Digital Twins – clause JOIN
titleSuffix: Azure Digital Twins
description: Documentation de référence sur la clause JOIN du langage de requête Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: ad9447966916098a5ca0834d593242e365b8a088
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246753"
---
# <a name="azure-digital-twins-query-language-reference-join-clause"></a>Informations de référence sur le langage de requête Azure Digital Twins : clause JOIN

Ce document contient des informations de référence sur la **clause JOIN** du [langage de requête Azure Digital Twins](concepts-query-language.md).

La clause `JOIN` est utilisée dans le langage de requête Azure Digital Twins dans le cadre de la [clause FROM](reference-query-clause-from.md) lorsque vous souhaitez interroger le graphique Azure Digital Twins.

Cette clause est facultative lors de l’interrogation.

## <a name="core-syntax-join--related"></a>Syntaxe principale : JOIN ... RELATED 
Dans Azure Digital Twins, les relations faisant partie de jumeaux numériques, et non d’entités indépendantes, le mot clé `RELATED` est utilisé dans les requêtes `JOIN` pour référencer l’ensemble de relations d’un certain type à partir de la collection de jumeaux (le type est spécifié à l’aide du champ **nom** de la relation dans sa [définition DTDL](concepts-models.md#basic-relationship-example)). Un nom de collection peut être attribué à l’ensemble de relations dans la requête.

La requête doit ensuite utiliser la clause `WHERE` pour spécifier le ou les jumeaux spécifiques utilisés pour prendre en charge la requête de relation, ce qui est effectué en filtrant sur la valeur `$dtId` du jumeau source ou cible.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="JoinSyntax":::

### <a name="example"></a>Exemple

La requête suivante sélectionne tous les jumeaux numériques associés au jumeau avec un ID *ABC* via une relation *contient*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="JoinExample":::

## <a name="multiple-joins"></a>Plusieurs clauses JOIN

Jusqu’à cinq clauses `JOIN` sont prises en charge dans une même requête, ce qui permet la traversée de plusieurs niveaux de relations à la fois.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MultiJoinSyntax":::

### <a name="example"></a>Exemple

La requête suivante est basée sur les salles contenant plusieurs panneaux lumineux, chacun contenant plusieurs ampoules. La requête obtient toutes les ampoules contenues dans les panneaux lumineux des salles 1 et 2.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MultiJoinExample":::

## <a name="limitations"></a>Limites

Les limites suivantes s’appliquent aux requêtes utilisant `JOIN`.
* [Limite de profondeur de cinq](#depth-limit-of-five)
* [Aucune sémantique OUTER JOIN](#no-outer-join-semantics)
* [Jumeau source requis](#twins-required)

Pour plus d’informations, consultez les sections suivantes.

### <a name="depth-limit-of-five"></a>Limite de profondeur de cinq

La profondeur de la traversée du graphe est restreinte à cinq niveaux `JOIN` autorisés par requête.

#### <a name="example"></a>Exemple

La requête suivante illustre le nombre maximal de clauses `JOIN` possibles dans une requête Azure Digital Twins. Elle obtient toutes les LightBulbs du Bâtiment 1.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MaxJoinExample":::

### <a name="no-outer-join-semantics"></a>Aucune sémantique OUTER JOIN

Les sémantiques `OUTER JOIN` ne sont pas prises en charge, ce qui signifie que si la relation a un rang égal à zéro, la « ligne » entière sera éliminée du jeu de résultats de sortie.

#### <a name="example"></a>Exemple

Considérez la requête suivante illustrant la traversée d’un bâtiment.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="NoOuterJoinExample":::

Si Building1 ne contient aucun étage, cette requête renvoie un jeu de résultats vide (plutôt qu’une ligne avec une valeur pour Bâtiment et `undefined` pour Étage).

### <a name="twins-required"></a>Jumeaux requis

Les relations dans Azure Digital Twins ne peuvent pas être interrogées en tant qu’entités indépendantes. Vous devez également fournir des informations sur le jumeau source à partir duquel provient la relation. Cette fonction est incluse dans le cadre de l'utilisation `JOIN` par défaut dans Azure Digital Twins via le mot clé `RELATED`. 

Les requêtes dotées d’une clause `JOIN` doivent également filtrer par propriété `$dtId` de jumeau dans la clause `WHERE` afin de clarifier le ou les jumeaux utilisés pour prendre en charge la requête de relation.