---
title: Métriques d’indexation Azure Cosmos DB
description: Découvrez comment obtenir et interpréter les métriques d’indexation dans Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: tisande
ms.openlocfilehash: f10208e83c9c7f23600285444d22ed5b5faf2488
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551973"
---
# <a name="indexing-metrics-in-azure-cosmos-db"></a>Métriques d'indexation dans Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB fournit des métriques d’indexation pour afficher à la fois les chemins indexés utilisés et les chemins indexés recommandés. Vous pouvez utiliser les métriques d’indexation pour optimiser les performances des requêtes, en particulier dans les cas où vous ne savez pas comment modifier la [stratégie d’indexation](../index-policy.md).

> [!NOTE]
> Les métriques d’indexation sont uniquement prises en charge dans le kit de développement logiciel (SDK) .NET (version 3.21.0 ou ultérieure) et le kit de développement logiciel (SDK) Java (version 4.19.0 ou ultérieure)

## <a name="enable-indexing-metrics"></a>Activer les métriques d’indexation

Vous pouvez activer les métriques d’indexation pour une requête en affectant à la propriété `PopulateIndexMetrics` la valeur `true`. En l'absence de toute spécification, `PopulateIndexMetrics` prend `false` comme valeur par défaut. Nous vous recommandons uniquement d’activer les métriques d’indexation pour le dépannage des performances des requêtes. Tant que vos requêtes et votre stratégie d’indexation restent les mêmes, il est peu probable que les métriques d’indexation changent. Au lieu de cela, nous vous recommandons d’identifier les requêtes coûteuses en surveillant la charge et la latence des requêtes à l’aide des journaux de diagnostic.

### <a name="net-sdk-example"></a>Exemple de Kit de développement logiciel (SDK) .NET

```csharp
    string sqlQueryText = "SELECT TOP 10 c.id FROM c WHERE c.Item = 'value1234' AND c.Price > 2";

    QueryDefinition query = new QueryDefinition(sqlQueryText);

    FeedIterator<Item> resultSetIterator = container.GetItemQueryIterator<Item>(
                query, requestOptions: new QueryRequestOptions
        {
            PopulateIndexMetrics = true
        });

    FeedResponse<Item> response = null;

    while (resultSetIterator.HasMoreResults)
        {
          response = await resultSetIterator.ReadNextAsync();
          Console.WriteLine(response.IndexMetrics);
        }
```

### <a name="example-output"></a>Exemple de sortie

Dans cet exemple de requête, nous observons les chemins utilisés `/Item/?` et `/Price/?`, et les index composites potentiels `(/Item ASC, /Price ASC)`.

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /Item/?
    Index Impact Score: High
    ---
    Index Spec: /Price/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /Item ASC, /Price ASC
    Index Impact Score: High
    ---
```

## <a name="utilized-indexed-paths"></a>Chemins indexés utilisés

Les index uniques utilisés et les index composites utilisés indiquent respectivement les chemins inclus et les index composites utilisés par la requête. Les requêtes peuvent utiliser plusieurs chemins indexés, ainsi qu’une combinaison de chemins inclus et d’index composites. Si un chemin indexé n’est pas utilisé, la suppression du chemin indexé n’aura aucun impact sur les performances de la requête.

Considérez la liste des chemins indexés utilisés comme preuve qu’une requête a utilisé ces chemins. Si vous ne savez pas si un nouveau chemin indexé améliore les performances des requêtes, vous devez essayer d’ajouter les nouveaux chemins indexés et vérifier si la requête les utilise.

## <a name="potential-indexed-paths"></a>Chemins indexés potentiels

Les index uniques potentiels et les index composites utilisés représentent respectivement les chemins inclus et les index composites qui, s’ils sont ajoutés, peuvent être utilisés par la requête. Si vous voyez des chemins indexés potentiels, vous devez envisager de les ajouter à votre stratégie d’indexation et observer s’ils améliorent les performances des requêtes.

Considérez la liste des chemins indexés potentiels comme des recommandations plutôt que des preuves concluantes qu’une requête utilisera un chemin indexé spécifique. Les chemins indexés potentiels ne sont pas une liste exhaustive des chemins indexés qu’une requête peut utiliser. En outre, il est possible que certains chemins indexés potentiels n’aient aucun impact sur les performances des requêtes. [Ajoutez les chemins indexés recommandés](how-to-manage-indexing-policy.md) et confirmez qu’ils améliorent les performances des requêtes.

> [!NOTE]
> Avez-vous des commentaires sur les métriques d’indexation ? Nous attendons vos remarques ! N’hésitez pas à partager vos commentaires directement avec l’équipe d’ingénierie Azure Cosmos DB :cosmosdbindexing@microsoft.com

## <a name="index-impact-score"></a>Score d’impact de l’index

Le score d’impact de l’index est la probabilité qu’un chemin indexé, sur la base de la forme de la requête, ait un impact significatif sur les performances des requêtes. En d’autres termes, le score d’impact de l’index est la probabilité que, sans ce chemin indexé spécifique, les frais de la requête RU aient été sensiblement plus élevés. 

Il existe deux résultats possibles d’impact d’index : **élevé** et **faible**. Si vous disposez de plusieurs chemins indexés potentiels, nous vous recommandons de vous concentrer sur les chemins indexés avec un score d’impact **élevé**.

Le seul critère utilisé dans le score d’impact de l’index est la forme de la requête. Par exemple, dans la requête ci-dessous, le chemin indexé `/name/?` se voit attribuer un score d’impact d’index **élevé** :

```sql
SELECT * 
FROM c
WHERE c.name = "Samer"
```

Impact réel en fonction de la nature des données. Si seuls quelques éléments correspondent au filtre `/name`, le chemin indexé améliore considérablement le coût de la charge en unités de requête. Toutefois, si la plupart des éléments correspondent au filtre `/name`, le chemin indexé peut ne pas améliorer les performances des requêtes. Dans chacun de ces cas, le chemin indexé `/name/?` se voit attribuer un score d’impact d’index **élevé**, car, sur la base de la forme de la requête, le chemin indexé a une probabilité élevée d’améliorer les performances des requêtes.

## <a name="additional-examples"></a>Exemples supplémentaires

### <a name="example-query"></a>Exemple de requête

```sql
SELECT c.id 
FROM c 
WHERE c.name = 'Tim' AND c.age > 15 AND c.town = 'Redmond' AND c.timestamp > 2349230183
```

### <a name="index-metrics"></a>Métriques d’indexation

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /name/?
    Index Impact Score: High
    ---
    Index Spec: /age/?
    Index Impact Score: High
    ---
    Index Spec: /town/?
    Index Impact Score: High
    ---
    Index Spec: /timestamp/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /name ASC, /town ASC, /age ASC
    Index Impact Score: High
    ---
    Index Spec: /name ASC, /town ASC, /timestamp ASC
    Index Impact Score: High
    ---
```
Ces métriques d’indexation montrent que la requête a utilisé les chemins indexés `/name/?`, `/age/?`, `/town/?` et `/timestamp/?`. Les métriques d’indexation indiquent également qu’il y a une forte probabilité que l’ajout d’index composites `(/name ASC, /town ASC, /age ASC)` et `(/name ASC, /town ASC, /timestamp ASC)` améliorera davantage les performances.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail l’indexation dans les articles suivants :

- [Vue d’ensemble de l’indexation](../index-overview.md)
- [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
