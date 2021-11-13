---
title: Informations de référence sur le langage de requête Azure Digital Twins – clause WHERE
titleSuffix: Azure Digital Twins
description: Documentation de référence sur la clause WHERE du langage de requête Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: bf70d1aa4b7a8a3cdfd9d8bcd90a9952e83271bc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232827"
---
# <a name="azure-digital-twins-query-language-reference-where-clause"></a>Informations de référence sur le langage de requête Azure Digital Twins : clause WHERE

Ce document contient des informations de référence sur la **clause WHERE** du [langage de requête Azure Digital Twins](concepts-query-language.md).

La clause WHERE est la dernière partie d’une requête. Elle est utilisée pour filtrer les éléments qui sont retournés en fonction de conditions spécifiques.

Cette clause est facultative lors de l’interrogation.

## <a name="core-syntax-where"></a>Syntaxe principale : WHERE

La clause WHERE est utilisée avec une condition booléenne pour filtrer les résultats de la requête. 

Une condition peut être une [fonction](reference-query-functions.md) qui prend la valeur d’un résultat booléen. Vous pouvez également créer votre propre instruction booléenne en utilisant les propriétés de jumeaux et de relations (accessibles avec `.`) avec un [opérateur](reference-query-operators.md) de comparaison ou de type « contient ».

### <a name="syntax"></a>Syntaxe

Avec des propriétés et des opérateurs :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereSyntax":::

Avec une fonction :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionSyntax":::

### <a name="arguments"></a>Arguments

Condition prenant une valeur `Boolean`.

### <a name="examples"></a>Exemples

Voici un exemple utilisant des propriétés et des opérateurs. La requête ci-dessous spécifie dans la clause WHERE de ne retourner que le jumeau dont la valeur `$dtId` est Room1.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereExample":::

Voici un exemple utilisant une fonction. La requête ci-dessous utilise la fonction `IS_OF_MODEL` pour spécifier, dans la clause WHERE, de ne retourner que les jumeaux ayant un modèle `dtmi:sample:Room;1`. Pour plus d’informations sur la fonction `IS_OF_MODEL`, consultez [Informations de référence sur le langage de requête Azure Digital Twins : Fonctions](reference-query-functions.md#is_of_model).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionExample":::