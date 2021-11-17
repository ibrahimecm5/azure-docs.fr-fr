---
title: Surveiller et déboguer à l’aide d’insights dans Azure Cosmos DB
description: Utilisez les métriques d’Azure Cosmos DB pour déboguer les problèmes courants et surveiller la base de données.
ms.author: esarroyo
author: StefArroyo
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 11/08/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ba9fac9fef3e418cc48bc3185ca91af89dd7946f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057059"
---
# <a name="monitor-and-debug-with-insights-in-azure-cosmos-db"></a>Surveiller et déboguer à l’aide d’insights dans Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB fournit des insights concernant le débit, le stockage, la cohérence, la disponibilité et la latence. Le portail Azure fournit une vue agrégée de ces métriques. Vous pouvez également consulter des métriques Azure Cosmos DB à partir de l’API Azure Monitor. Les valeurs de dimension des métriques, comme le nom du conteneur, ne respectent pas la casse. Vous devez donc utiliser une comparaison ne respectant pas la casse lorsque vous comparez les chaînes sur ces valeurs de dimension. Pour savoir comment consulter des métriques à partir d’Azure Monitor, consultez l’article [Obtenir des métriques d’Azure Monitor](./monitor-cosmos-db.md).

Cet article présente des cas d’utilisation courants et montre comment utiliser les insights d’Azure Cosmos DB pour analyser et déboguer ces problèmes. Par défaut, les insights de métriques sont collectées toutes les cinq minutes et sont pendant sept jours.

## <a name="view-insights-from-azure-portal"></a>Afficher les insights du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre compte Azure Cosmos DB.

1. Vous pouvez afficher les métriques de votre compte dans le volet **Métriques** ou **Insights**.

   * **Métriques** : ce volet fournit des métriques numériques collectées à intervalles réguliers, qui décrivent un certain aspect d’un système à un moment donné. Par exemple, vous pouvez afficher et surveiller la [métrique de latence côté serveur](monitor-server-side-latency.md), la [métrique d’utilisation d’unités de requête normalisée](monitor-normalized-request-units.md), etc.

   * **Insights** : ce volet offre une expérience de surveillance personnalisée pour Azure Cosmos DB. Il utilise les métriques et journaux collectés dans Azure Monitor, et affiche une vue agrégée de votre compte.

1. Ouvrez le volet **Insights**. Par défaut, le volet Insights affiche les métriques de gestion du débit, des demandes, du stockage, de la disponibilité, de la latence, du système et du compte pour chaque conteneur dans votre compte. Vous pouvez sélectionner l’**Intervalle de temps**, la **Base de données** et le **Conteneur** pour lesquels vous voulez afficher des insights. L’onglet **Vue d’ensemble** montre l’utilisation des unités de requête par seconde (UR/s), l’utilisation des données, l’utilisation des index, les demandes limitées et la consommation d’UR/s normalisée pour la base de données et le conteneur sélectionnés.

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Métriques de performances Cosmos DB dans le Portail Azure" lightbox="./media/use-metrics/performance-metrics.png" :::

1. Les métriques suivantes sont disponibles dans le volet **Insights** :

   * **Débit** : ce volet affiche le nombre total d’unités de requête consommées ou ayant échoué (code de réponse 429) en raison d’un dépassement des capacités de débit ou de stockage approvisionnées pour le conteneur.

   * **Requêtes** : cet onglet affiche le nombre total de requêtes traitées par code d’état, type d’opération et nombre de requêtes ayant échoué (code de réponse 429). Les requêtes échouent en cas de dépassement du débit ou la capacité de stockage approvisionnés pour le conteneur.

   * **Stockage** : cet onglet affiche la taille des données et l’utilisation des index sur la période sélectionnée.

   * **Disponibilité** : cet onglet affiche le pourcentage de requêtes abouties par rapport au nombre total de requêtes par heure. Le taux de réussite est défini par les contrats SLA d’Azure Cosmos DB.

   * **Latence** : cet onglet affiche la latence de lecture et d’écriture observée par Azure Cosmos DB dans la région où votre compte opère. Vous pouvez visualiser la latence entre les régions pour un compte géorépliqué. Vous pouvez également visualiser la latence côté serveur pour différentes opérations. Cette métrique ne représente pas la latence des demandes de bout en bout.

   * **Système** : cet onglet affiche le nombre de demandes de métadonnées servies par la partition principale. Elle permet également d’identifier les demandes limitées.

   * **Gestion du compte** : cet onglet affiche les métriques des activités de gestion du compte, telles que la création, la suppression, les mises à jour de clés, le réseau et les paramètres de réplication.

Les sections suivantes décrivent des scénarios courants où vous pouvez utiliser les métriques Azure Cosmos DB.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Connaître le nombre de requêtes ayant abouti ou ayant provoqué une erreur

Pour commencer, accédez au [portail Azure](https://portal.azure.com), puis au panneau **Insights**. Dans ce panneau, ouvrez l’onglet **Requêtes** qui affiche un graphique présentant le nombre total de requêtes segmentées par code d’état et type d’opération. Pour plus d’informations sur les codes d’état HTTP, consultez [Codes d’état HTTP pour Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Le code d’état d’erreur le plus courant est 429 (limitation du débit). Cette erreur signifie que les requêtes envoyées à Azure Cosmos DB sont supérieures au débit provisionné. Dans ce cas, la solution la plus courante consiste à [effectuer une montée en puissance des unités de requête](./set-throughput.md) pour une collection donnée.

:::image type="content" source="media/use-metrics/request-count.png" alt-text="Nombre de requêtes par minute" lightbox= "media/use-metrics/request-count.png":::

## <a name="determine-the-throughput-consumption-by-a-partition-key-range"></a>Déterminer la consommation de débit par une plage de clés de partition

Il est essentiel d’avoir une bonne cardinalité des clés de partition pour vos applications évolutives. Pour déterminer la distribution du débit d’un conteneur partitionné, ventilée par ID de plage de clés de partition, accédez au volet **Insights**. Ouvrez l’onglet **Débit**. La consommation d’UR/s normalisée dans les différentes plages de clés de partition est présentée dans le graphique.

:::image type="content" source="media/use-metrics/throughput-consumption-partition-key-range.png" alt-text="Consommation de débit normalisée par ID de plage de clés de partition" lightbox="media/use-metrics/throughput-consumption-partition-key-range.png":::

Ce graphique vous permet de déterminer s’il existe une partition chaude. Une distribution inégale du débit peut aboutir à une *forte utilisation* de certaines partitions. Dans ce cas, une limitation des requêtes peut se produire et nécessiter un repartitionnement. Une fois que vous avez identifié la clé de partition qui est à l’origine du déséquilibre, il est possible que vous deviez repartitionner votre conteneur avec une clé de partition mieux distribuée. Pour plus d’informations sur le partitionnement dans Azure Cosmos DB, consultez [Partitionner et mettre à l’échelle dans Azure Cosmos DB](./partitioning-overview.md).

## <a name="determine-the-data-and-index-usage"></a>Déterminer l’utilisation des données et des index

Il est important de déterminer la distribution du stockage de tout conteneur partitionné par utilisation de données, d’index et de documents. Vous pouvez réduire l’utilisation des index, maximiser l’utilisation des données et optimiser vos requêtes. Pour obtenir ces données, accédez au volet **Insights** et ouvrez l’onglet **Stockage** :

:::image type="content" source="media/use-metrics/data-index-consumption.png" alt-text="Consommation de données, d’index et de documents" lightbox="media/use-metrics/data-index-consumption.png" :::

## <a name="compare-data-size-against-index-size"></a>Comparer la taille des données et la taille de l’index

Dans Azure Cosmos DB, la consommation totale du stockage correspond à la somme de la taille des données et de la taille de l’index. En règle générale, la taille de l’index correspond à une fraction de la taille des données. Pour plus d’informations, consultez l’article [Taille de l’index](index-policy.md#index-size). Dans le panneau Métriques du [portail Azure](https://portal.azure.com), l’onglet Stockage présente la répartition de la consommation du stockage entre les données et l’index.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Si vous souhaitez conserver de l’espace d’index, vous pouvez ajuster la [stratégie d’indexation](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Résoudre les problèmes liés à l’exécution lente des requêtes

Dans les SDK de l’API SQL, Azure Cosmos DB fournit des statistiques relatives à l’exécution des requêtes.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* fournit des informations détaillées sur la durée d’exécution de chaque composant de la requête. Les analyses constituent la cause racine la plus courante de la longueur d’exécution des requêtes, ce qui signifie que la requête n’a pas pu exploiter les index. Ce problème peut être résolu avec une meilleure condition de filtre.

## <a name="next-steps"></a>Étapes suivantes

Vous venez de découvrir comment superviser et déboguer les problèmes à l’aide des métriques fournies dans le portail Azure. Vous souhaiterez peut-être en savoir plus sur l’amélioration des performances de la base de données en lisant les articles suivants :

* Pour savoir comment consulter des métriques à partir d’Azure Monitor, consultez l’article [Obtenir des métriques d’Azure Monitor](./monitor-cosmos-db.md). 
* [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md)
* [Conseils sur les performances pour Azure Cosmos DB](performance-tips.md)