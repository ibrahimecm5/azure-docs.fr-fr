---
title: 'Langage de requête Azure Cosmos DB : COUNT'
description: En savoir plus sur la fonction système Count (COUNT) SQL dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: edc3c824bc9ac08e325259c0b5f736867f877062
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525671"
---
# <a name="count-azure-cosmos-db"></a>COUNT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Cette fonction système renvoie le nombre de valeurs de l’expression.
  
## <a name="syntax"></a>Syntaxe
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*scalar_expr*  
   Peut être toute expression scalaire.
  
## <a name="return-types"></a>Types de retour
  
Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
L’exemple suivant renvoie le nombre total d’éléments d’un conteneur :
  
```sql
SELECT COUNT(1)
FROM c
``` 
COUNT peut utiliser n’importe quelle expression scalaire en tant qu’entrée. La requête ci-dessous produit des résultats équivalents :

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Remarques

Cette fonction système tire parti d’un [index de plage](../index-policy.md#includeexclude-strategy) pour toutes les propriétés du filtre de la requête.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques dans Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système dans Azure Cosmos DB](sql-query-system-functions.md)
- [Fonctions d’agrégation dans Azure Cosmos DB](sql-query-aggregate-functions.md)