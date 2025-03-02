---
title: 'Langage de requête Azure Cosmos DB : MAX'
description: En savoir plus sur la fonction système Max (MAX) SQL dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b14ca6a61644ab6f668015af62cdea899320c12d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533257"
---
# <a name="max-azure-cosmos-db"></a>MAX (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Cette fonction d’agrégation retourne la valeur maximale des valeurs de l’expression.
  
## <a name="syntax"></a>Syntaxe
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Arguments

*scalar_expr*  
   Est une expression scalaire. 
  
## <a name="return-types"></a>Types de retour
  
Retourne une expression scalaire.  
  
## <a name="examples"></a>Exemples
  
L’exemple suivant retourne la valeur maximale de `propertyA` :
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Notes

Cette fonction système bénéficiera d’un [index de plage](../index-policy.md#includeexclude-strategy). Les arguments dans `MAX` peuvent être de type nombre, chaîne, booléen ou null. Toutes les valeurs non définies seront ignorées.

Lorsque vous comparez des données de types différents, l’ordre de priorité suivant est utilisé (dans l’ordre décroissant) :

- chaîne
- nombre
- boolean
- null

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques dans Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système dans Azure Cosmos DB](sql-query-system-functions.md)
- [Fonctions d’agrégation dans Azure Cosmos DB](sql-query-aggregate-functions.md)