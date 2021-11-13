---
title: Informations de référence sur le langage de requête Azure Digital Twins – clause MATCH
titleSuffix: Azure Digital Twins
description: Documentation de référence sur la clause MATCH du langage de requête Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 10/07/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 8c553d0df1f194c1de9f77e21341cb1022f98158
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270762"
---
# <a name="azure-digital-twins-query-language-reference-match-clause"></a>Informations de référence sur le langage de requête Azure Digital Twins : clause MATCH

Ce document contient des informations de référence sur la **clause MATCH** du [langage de requête Azure Digital Twins](concepts-query-language.md).

La clause `MATCH` est utilisée dans le langage de requête Azure Digital Twins dans le cadre de la [clause FROM](reference-query-clause-from.md). `MATCH` vous permet de spécifier le modèle à suivre pour parcourir les relations dans le graphe Azure Digital Twins (également connu sous le nom de modèle de requête « saut de variable »).

Cette clause est facultative lors de l’interrogation.

## <a name="core-syntax-match"></a>Syntaxe principale : MATCH

`MATCH` prend en charge toute requête qui trouve un chemin entre des jumeaux avec un nombre imprévisible de sauts, en fonction de certaines conditions de relation. 

La **condition de relation** peut inclure un ou plusieurs des détails suivants :
* [Direction de la relation](#specify-relationship-direction) (de gauche à droite, de droite à gauche ou non directionnelle)
* [Nom de la relation](#specify-relationship-name) (nom unique ou liste de possibilités)
* [Nombre de « sauts »](#specify-number-of-hops) d’un jumeau à un autre (nombre exact ou plage)
* [Une affectation de variable de requête](#assign-query-variable-to-relationship-and-specify-relationship-properties) pour représenter la relation au sein du texte de la requête. Cela vous permettra également de filtrer les propriétés de relation.

Une requête qui inclut une clause `MATCH` doit également utiliser la [clause WHERE](reference-query-clause-where.md) pour spécifier `$dtId` pour au moins l’un des jumeaux qu’elle référence.

>[!NOTE]
>`MATCH` est un surensemble de toutes les requêtes `JOIN` qui peuvent être effectuées dans le magasin des requêtes.

### <a name="syntax"></a>Syntax

Voici la syntaxe `MATCH` de base. 

Les valeurs d’espace réservé indiquées dans la clause `MATCH` que vous devez remplacer par vos valeurs sont `twin_1`, `relationship_condition` et `twin_2`. Les valeurs d’espace réservé indiquées dans la clause `WHERE` que vous devez remplacer par vos valeurs sont `twin_or_twin_collection` et `twin_ID`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchSyntax":::

Vous pouvez omettre le nom de l’un des jumeaux pour permettre à n’importe quel nom de jumeau de fonctionner à cet endroit.

Vous pouvez également modifier le nombre de conditions de relation pour avoir plusieurs conditions de relation [chaînées](#combine-match-operations) ou aucune condition de relation :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNodeSyntax":::

Pour plus d’informations sur chaque type de condition de relation et sur la manière de les combiner, consultez les autres sections de ce document.

### <a name="example"></a>Exemple

Voici un exemple de requête qui utilise `MATCH`.

La requête spécifie une [direction de relation](#specify-relationship-direction), puis recherche des jumeaux de bâtiment et de capteur où :
* Le capteur est ciblé par toute relation d’un bâtiment dont la valeur `$dtId` est Building21. 
* Le capteur indique une température supérieure à 50.
Le bâtiment et le capteur sont tous les deux inclus dans le résultat de la requête.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchExample":::

## <a name="specify-relationship-direction"></a>Spécifier la direction de la relation

Utilisez la condition de relation dans la clause `MATCH` pour spécifier une direction de relation entre les jumeaux. Les directions possibles sont les suivants : de gauche à droite, de droite à gauche ou non directionnelle. Les relations cycliques sont automatiquement détectées, afin qu’une relation ne soit traversée qu’une seule fois.

> [!NOTE]
> Il est possible de représenter des relations bidirectionnelles en utilisant un [chaînage](#combine-match-operations).

### <a name="syntax"></a>Syntax

>[!NOTE]
> Les exemples de cette section se concentrent sur la direction de la relation. Ils ne spécifient pas de noms de relation, leur valeur par défaut correspond à un saut unique et ils n’affectent pas de variables de requête aux relations. Pour obtenir des instructions sur l’utilisation de ces autres conditions, consultez [Spécifier le nom de la relation](#specify-relationship-name), [Spécifier le nombre de sauts](#specify-number-of-hops) et [Affecter une variable de requête à une relation](#assign-query-variable-to-relationship-and-specify-relationship-properties). Pour plus d’informations sur l’utilisation simultanée de plusieurs de ces conditions dans la même requête, consultez [Combiner des opérations MATCH](#combine-match-operations).

Les descriptions de relations directionnelles utilisent une représentation visuelle d’une flèche pour indiquer la direction de la relation. La flèche inclut un espace réservé par des crochets (`[]`) à un éventuel [nom de relation](#specify-number-of-hops). 

Cette section présente la syntaxe de différentes directions de relation. Les valeurs d’espace réservé que vous devez remplacer par vos valeurs sont `source_twin` et `target_twin`.

Pour une relation de **gauche à droite**, utilisez la syntaxe suivante.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRSyntax":::

Pour une relation de **droite à gauche**, utilisez la syntaxe suivante.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLSyntax":::

Pour une relation **non directionnelle**, utilisez la syntaxe suivante. Celle-ci ne spécifie pas de direction pour la relation, si bien que des relations de toute direction sont incluses dans le résultat.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDSyntax":::

>[!TIP]
>Les requêtes non directionnelles nécessitent un traitement supplémentaire, susceptible d’entraîner une latence et un coût accrus.

### <a name="examples"></a>Exemples

Le premier exemple montre une traversée directionnelle de **gauche à droite**. Cette requête recherche des jumeaux *room* (salle) et *factory* (usine) où :
* *room* cible *factory* (quel que soit le nom de la relation).
* *room* présente une valeur de température supérieure à 50.
* *factory* a la valeur `$dtId` définie sur 'ABC'.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRExample":::

L’exemple suivant montre une traversée directionnelle de **gauche à droite**. Cette requête ressemble à celle ci-dessus, mais la direction de la relation entre *room* et *factory* est inversée. Cette requête recherche des jumeaux *room* (salle) et *factory* (usine) où :
* *factory* cible *room* (quel que soit le nom de la relation).
* *factory* a la valeur `$dtId` définie sur 'ABC'.
* *room* présente une valeur de température supérieure à 50.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLExample":::

L’exemple suivant montre une traversée **non directionnelle**. Cette requête recherche des jumeaux *room* (salle) et *factory* (usine) où :
* *room* et *factory* partagent n’importe quel nom de relation, dans l’une ou l’autre direction.
* *factory* a la valeur `$dtId` définie sur 'ABC'.
* *room* présente une valeur de taux d’humidité supérieure à 70.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDExample":::

## <a name="specify-relationship-name"></a>Spécifier le nom de la relation

Éventuellement, vous pouvez utiliser la condition de relation dans la clause `MATCH` pour spécifier les noms des relations entre les jumeaux. Vous pouvez spécifier un nom unique ou une liste de noms possibles. Le nom de relation facultatif est inclus dans le cadre de la [syntaxe de flèche pour spécifier la direction de la relation](#specify-relationship-direction).

Si vous ne fournissez pas de nom de relation, la requête inclut tous les noms de relation par défaut.

>[!TIP]
>La spécification de noms de relation dans la requête peut améliorer les performances et rendre les résultats plus prédictibles.

### <a name="syntax"></a>Syntax

>[!NOTE]
> Les exemples de cette section se concentrent sur le nom de la relation. Ils présentent tous des relations non directionnelles, leur valeur par défaut correspond à un saut unique et ils n’affectent pas de variables de requête aux relations. Pour obtenir des instructions sur l’utilisation de ces autres conditions, consultez [Spécifier la direction de la relation](#specify-relationship-direction), [Spécifier le nombre de sauts](#specify-number-of-hops) et [Affecter une variable de requête à une relation](#assign-query-variable-to-relationship-and-specify-relationship-properties). Pour plus d’informations sur l’utilisation simultanée de plusieurs de ces conditions dans la même requête, consultez [Combiner des opérations MATCH](#combine-match-operations).

Spécifiez le nom d’une relation à traverser dans la clause `MATCH` entre crochets (`[]`). Cette section décrit la syntaxe de la spécification de relations nommées.

Pour un **nom unique**, utilisez la syntaxe suivante. Les valeurs d’espace réservé que vous devez remplacer par vos valeurs sont `twin_1`, `relationship_name` et `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleSyntax":::

Pour **plusieurs noms possibles**, utilisez la syntaxe suivante. Les valeurs d’espace réservé que vous devez remplacer par vos valeurs sont `twin_1`, `relationship_name_option_1`, `relationship_name_option_2`, `twin_2` et la remarque indiquant de continuer le modèle selon les besoins en termes de nombre de noms de relation à entrer.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiSyntax":::

(Par défaut) Pour **ne pas spécifier** de nom, laissez les crochets vides, comme ceci :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllSyntax":::

### <a name="examples"></a>Exemples

L’exemple suivant montre un **nom de relation unique**. Cette requête recherche des jumeaux *building* (bâtiment) et *sensor* (capteur) où :
* *building* a une relation « contient » avec *sensor* (dans l’une ou l’autre direction).
* *building* a la valeur `$dtId` définie sur 'Seattle21'.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleExample":::

L’exemple suivant montre **plusieurs noms de relation possibles**. Cette requête ressemble à celle ci-dessus, mais plusieurs noms de relation possibles sont inclus dans le résultat. Cette requête recherche des jumeaux *building* (bâtiment) et *sensor* (capteur) où :
* *building* a une relation « contains » ou « isAssociatedWith » avec *sensor* (dans l’une ou l’autre direction).
* *building* a la valeur `$dtId` définie sur 'Seattle21'.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiExample":::

L’exemple suivant n’a **aucun nom de relation spécifié**. Ainsi, les relations sont incluses dans le résultat de la requête, quel que soit leur nom. Cette requête recherche des jumeaux *building* (bâtiment) et *sensor* (capteur) où :
* *building* a une relation avec *sensor* quel que soit son nom (dans l’une ou l’autre direction).
* *building* a la valeur `$dtId` définie sur 'Seattle21'.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllExample":::

## <a name="specify-number-of-hops"></a>Spécifier le nombre de sauts

Éventuellement, vous pouvez utiliser la condition de relation dans la clause `MATCH` pour spécifier le nombre de sauts pour les relations entre les jumeaux. Vous pouvez spécifier un nombre exact ou une plage. La valeur facultative est incluse dans le cadre de la [syntaxe de flèche pour spécifier la direction de la relation](#specify-relationship-direction).

Si vous n’indiquez pas le nombre de sauts, ce dernier est égal à un par défaut dans la requête.

>[!IMPORTANT]
>Si vous spécifiez un nombre de sauts supérieur à un, vous ne pouvez pas [affecter une variable de requête à la relation](#assign-query-variable-to-relationship-and-specify-relationship-properties). Une seule de ces conditions peut être utilisée au sein de la même requête. 

### <a name="syntax"></a>Syntax

>[!NOTE]
>Les exemples de cette section se concentrent sur le nombre de sauts. Ils présentent tous des relations non directionnelles sans spécifier de noms. Pour obtenir des instructions sur l’utilisation de ces autres conditions, consultez [Spécifier la direction de la relation](#specify-relationship-direction) et [Spécifier le nom de la relation](#specify-relationship-name). Pour plus d’informations sur l’utilisation simultanée de plusieurs de ces conditions dans la même requête, consultez [Combiner des opérations MATCH](#combine-match-operations).

Spécifiez le nombre de sauts à traverser dans la clause `MATCH` entre crochets (`[]`).

Pour spécifier un **nombre exact de sauts**, utilisez la syntaxe suivante. Les valeurs d’espace réservé que vous devez remplacer par vos valeurs sont `twin_1`, `number_of_hops` et `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactSyntax":::

Pour spécifier une **plage de sauts**, utilisez la syntaxe suivante. Les valeurs d’espace réservé que vous devez remplacer par vos valeurs sont `twin_1`, `starting_limit`, `ending_limit` et `twin_2`. La limite de début **n’est pas** incluse dans la plage, alors que la limite de fin **est** incluse.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeSyntax":::

Vous pouvez aussi omettre la limite de début pour indiquer « toute valeur allant jusqu’à » la limite de fin (incluse). Une limite de fin doit toujours être fournie.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingSyntax":::

(Par défaut) Pour spécifier **un seul saut** par défaut, laissez les crochets vides, comme ceci :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneSyntax":::

### <a name="examples"></a>Exemples

L’exemple suivant spécifie un **nombre exact de sauts**. La requête va uniquement retourner les relations entre les jumeaux *floor* (étage) et *room* (salle) dont le nombre de sauts est strictement égal à 3.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactExample":::

L’exemple suivant spécifie une **plage de sauts**. La requête va retourner les relations entre les jumeaux *floor* et *room* comprises entre 1 et 3 sauts (ce qui signifie que le nombre de sauts est égal à 2 ou 3).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeExample1":::

Vous pouvez aussi présenter une plage en ne fournissant qu’une seule limite. Dans l’exemple suivant, la requête va retourner les relations entre les jumeaux *floor* et *room* comprenant au maximum 2 sauts (ce qui signifie que le nombre de sauts est égal à 1 ou 2).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingExample":::

L’exemple suivant n’a aucun nombre spécifié de sauts. Donc, par défaut, **un seul saut** entre les jumeaux *floor* et *room* est défini.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneExample":::

## <a name="assign-query-variable-to-relationship-and-specify-relationship-properties"></a>Affecter une variable de requête à une relation (et spécifier des propriétés de relation)

Vous pouvez éventuellement affecter une variable de requête à la relation référencée dans la clause `MATCH`, pour pouvoir y faire référence par son nom dans le texte de la requête.

Ainsi, vous avez alors la possibilité de filtrer les propriétés de relation dans votre clause `WHERE`.

>[!IMPORTANT]
> L’affectation d’une variable de requête à la relation est prise en charge uniquement quand la requête spécifie un seul saut. Au sein d’une requête, vous devez choisir entre la spécification d’une variable de relation et la [spécification d’un nombre supérieur de sauts](#specify-number-of-hops).

### <a name="syntax"></a>Syntax

>[!NOTE]
>Les exemples de cette section se concentrent sur une variable de requête pour la relation. Ils présentent tous des relations non directionnelles sans spécifier de noms. Pour obtenir des instructions sur l’utilisation de ces autres conditions, consultez [Spécifier la direction de la relation](#specify-relationship-direction) et [Spécifier le nom de la relation](#specify-relationship-name). Pour plus d’informations sur l’utilisation simultanée de plusieurs de ces conditions dans la même requête, consultez [Combiner des opérations MATCH](#combine-match-operations).

Pour affecter une variable de requête à la relation, placez le nom de la variable entre crochets (`[]`). Les valeurs d’espace réservé indiquées ci-dessous que vous devez remplacer par vos valeurs sont `twin_1`, `relationship_variable` et `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableSyntax":::

### <a name="examples"></a>Exemples

L’exemple suivant affecte une variable de requête 'r' à la relation. Plus loin, dans la clause `WHERE`, l’exemple utilise la variable pour spécifier que la relation *rel* doit avoir une propriété de nom dont la valeur est 'child'.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableExample":::

## <a name="combine-match-operations"></a>Combiner des opérations MATCH

Vous pouvez combiner plusieurs conditions de relation dans la même requête. Vous pouvez aussi chaîner plusieurs conditions de relation pour exprimer des relations bidirectionnelles ou d’autres combinaisons plus larges.

### <a name="syntax"></a>Syntax

Dans une requête unique, vous pouvez combiner la [direction de la relation](#specify-relationship-direction), le [nom de la relation](#specify-number-of-hops) et **un** des deux [nombres de sauts](#specify-number-of-hops) ou [une affectation de variable de requête](#assign-query-variable-to-relationship-and-specify-relationship-properties).

Les exemples de syntaxe suivants montrent comment ces attributs peuvent se combiner. Vous pouvez aussi exclure les informations facultatives présentées dans les espaces réservés pour omettre cette partie de la condition.

Pour spécifier la **direction de la relation, le nom de la relation et le nombre de sauts** au sein d’une seule requête, utilisez la syntaxe suivante dans la condition de relation. Les valeurs d’espace réservé que vous devez remplacer par vos valeurs sont `twin_1` et `twin_2`, `optional_left_angle_bracket` et `optional_right_angle_bracket`, `relationship_name(s)`, puis `number_of_hops`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsSyntax":::

Pour spécifier la **direction de la relation, le nom de la relation et une variable de requête pour la relation** au sein d’une seule requête, utilisez la syntaxe suivante dans la condition de relation. Les valeurs d’espace réservé que vous devez remplacer par vos valeurs sont `twin_1` et `twin_2`, `optional_left_angle_bracket` et `optional_right_angle_bracket`, `relationship_variable`, puis `relationship_name(s)`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableSyntax":::

>[!NOTE]
>Conformément aux options de [spécification de la direction de la relation](#specify-relationship-direction), vous devez choisir entre un chevron gauche pour une relation de gauche à droite et un chevron droit pour une relation de droite à gauche. Vous ne pouvez pas inclure les deux dans la même flèche, mais vous pouvez représenter des relations bidirectionnelles par un chaînage.

Vous pouvez **chaîner** plusieurs conditions de relation ensemble, comme dans cet exemple. Les valeurs d’espace réservé que vous devez remplacer par vos valeurs sont `twin_1`, toutes les instances de `relationship_condition` et `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

### <a name="examples"></a>Exemples

Voici un exemple qui combine la **direction de la relation, le nom de la relation et le nombre de sauts**. La requête suivante trouve les jumeaux *floor* et *room*, pour lesquels la relation entre *floor* et *room* remplit ces conditions :
* La relation est de gauche à droite, avec *floor* comme source et *room* comme cible.
* Le nom de la relation comporte 'contains' ou 'isAssociatedWith'.
* La relation comporte 4 ou 5 sauts.

La requête spécifie aussi que le jumeau *floor* a la valeur `$dtId` définie sur 'thermostat-15'.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsExample":::

Voici un exemple qui combine la **direction de la relation, le nom de la relation et une variable de requête nommée pour la relation**. La requête suivante trouve les jumeaux *floor* et *room*, pour lesquels la relation entre *floor* et *room* est affectée à une variable de requête *r* et remplit ces conditions :
* La relation est de gauche à droite, avec *floor* comme source et *room* comme cible.
* Le nom de la relation comporte 'contains' ou 'isAssociatedWith'.
* La relation, qui reçoit une variable de requête *r*, a une propriété de longueur égale à 10.

La requête spécifie aussi que le jumeau *floor* a la valeur `$dtId` définie sur 'thermostat-15'.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableExample":::

L’exemple suivant illustre des conditions de relation **chaînées**. La requête trouve les jumeaux *floor*, *cafe* (café) et *room*, où :
* La relation entre *floor* et *room* remplit ces conditions :
    - La relation est de gauche à droite, avec *floor* comme source et *cafe* comme cible.
    - Le nom de la relation comporte 'contains' ou 'isAssociatedWith'.
    - La relation, qui reçoit la variable de requête *r*, a une propriété de longueur égale à 10.
* La relation entre *cafe* et *room* remplit ces conditions :
    - La relation est de droite à gauche, avec *room* comme source et *cafe* comme cible.
    - Le nom de la relation comporte soit 'has', soit 'includes'.
    - La relation compte jusqu’à 3 (à savoir 1, 2 ou 3) sauts.

La requête spécifie aussi que le jumeau *floor* a une valeur `$dtId` définie sur 'thermostat-15' et que le jumeau *cafe* a une température égale à 55.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainExample":::

Vous pouvez aussi utiliser des conditions de relation chaînées pour exprimer des **relations bidirectionnelles**. La requête suivante recherche les jumeaux *floor*, *room* et *building*, où :
* La relation entre *building* et *floor* remplit ces conditions :
    - La relation est de gauche à droite, avec *building* comme source et *floor* comme cible.
    - Le nom de la relation comporte 'isAssociatedWith'.
    - La relation reçoit une variable de requête *r1*.
* La relation entre *floor* et *room* remplit ces conditions :
    - La relation est de droite à gauche, avec *room* comme source et *floor* comme cible.
    - Le nom de la relation comporte 'isAssociatedWith'.
    - La relation reçoit une variable de requête *r2*.

La requête spécifie aussi que le jumeau *building* a une valeur `$dtId` définie sur 'building-3' et que le jumeau *room* présente une température supérieure à 50.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainBDExample":::

## <a name="limitations"></a>Limitations

Les limites suivantes s’appliquent aux requêtes qui utilisent `MATCH` :
* Une seule expression `MATCH` est prise en charge par instruction de requête.
* `$dtId` est nécessaire dans la clause `WHERE`.
* L’affectation d’une variable de requête à la relation est prise en charge uniquement quand la requête spécifie un seul saut.
* Le nombre maximal de sauts pris en charge dans une requête s’élève à 10.