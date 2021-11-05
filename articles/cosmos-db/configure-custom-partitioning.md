---
title: Configurer le partitionnement personnalisé pour partitionner les données du magasin analytique (version préliminaire)
description: Découvrez comment déclencher le partitionnement personnalisé à partir du notebook Azure Synapse Spark à l’aide d’Azure Synapse Link pour Azure Cosmos DB. Ce tutoriel explique les options de configuration.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7848a9b578c6054ef1d3deb29bf4c2bf92b9f68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096536"
---
# <a name="configure-custom-partitioning-to-partition-analytical-store-data-preview"></a>Configurer le partitionnement personnalisé pour partitionner les données du magasin analytique (version préliminaire)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Pour utiliser le partitionnement personnalisé, vous devez activer Azure Synapse Link sur votre compte Azure Cosmos DB. Pour plus d’informations, consultez [Configurer Azure Synapse Link](configure-synapse-link.md). L’exécution d’un partitionnement personnalisé peut être déclenchée à partir du bloc-notes Azure Synapse Spark à l’aide d’Azure Synapse Link pour Azure Cosmos DB.

> [!IMPORTANT]
> La fonctionnalité de partitionnement personnalisé est actuellement en version préliminaire publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Les comptes Azure Cosmos DB doivent avoir un lien Azure Synapse Link activé pour tirer parti du partitionnement personnalisé. Le partitionnement personnalisé est actuellement pris en charge pour Azure Synapse Spark 2.0 uniquement.

Les options de configuration obligatoires requises pour déclencher l’exécution d’un partitionnement personnalisé sont les suivantes :

* `spark.cosmos.asns.execute.partitioning` : valeur booléenne qui déclenche l’exécution du partitionnement personnalisé. La valeur par défaut est false.

* `spark.cosmos.asns.partition.keys` : clés de partition(s) utilisant une chaîne au format DDL. Par exemple : *ReadDate String*.

* `spark.cosmos.asns.basePath` : chemin d’accès de base pour le magasin partitionné sur le compte de stockage principal Synapse.

> [!NOTE]
> Si vous choisissez plusieurs clés de partition, vous pouvez accéder à ces enregistrements à partir du même magasin partitionné avec le basePath qui indique la clé.

Voici les options de configuration facultatives que vous pouvez utiliser pour déclencher l’exécution d’un partitionnement personnalisé :

* `spark.cosmos.asns.merge.partitioned.files` : valeur booléenne qui permet à de créer un seul fichier par valeur de partition par exécution. La valeur par défaut est false.

* `spark.cosmos.asns.partitioning.maxRecordsPerFile` : nombre maximal d’enregistrements dans un fichier à partition unique dans le magasin partitionné. Si cette configuration et `spark.cosmos.asns.merge.partitioned.files` sont spécifiés, les nouveaux fichiers sont créés une fois que le nombre d’enregistrements dépasse la valeur maxRecordsPerFile. Cette configuration est généralement nécessaire uniquement pour le partitionnement initial pour les collections plus volumineuses. La valeur par défaut est 1 000 000.

  Lorsque vous définissez maxRecordsPerFile, mais que vous ne configurez pas `spark.cosmos.asns.merge.partitioned.files`, les enregistrements peuvent être fractionnés entre les fichiers avant d’atteindre le maxRecordsPerFile. Le fractionnement du fichier dépend également du parallélisme disponible sur le pool.

* `spark.cosmos.asns.partitioning.shuffle.partitions` : contrôle le parallélisme lors des écritures partitionnées dans le magasin partitionné. Cette configuration est nécessaire uniquement pour le partitionnement initial pour les collections plus volumineuses. Elle est définie sur le nombre de cœurs disponibles sur le pool Spark. La valeur par défaut est 200. Une valeur inférieure peut gaspiller des ressources si le pool n’est pas utilisé pour d’autres charges de travail. Une valeur plus élevée ne provoque généralement pas de problèmes, car certaines tâches se terminent en amont et peuvent démarrer davantage de tâches pendant l’exécution des requêtes lentes. Si vous souhaitez que le travail de partitionnement se termine plus rapidement, il est recommandé d’augmenter la taille du pool.

# <a name="python"></a>[Python](#tab/python)

```python
spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.execute.partitioning", "true") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .option("spark.cosmos.asns.merge.partitioned.files", "true") \
    .option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000") \
    .option("spark.cosmos.asns.partitioning.shuffle.partitions", "400") \
    .load()
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
spark.read.
    format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.asns.execute.partitioning", "true").
    option("spark.cosmos.asns.partition.keys", "readDate String").
    option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
    option("spark.cosmos.asns.merge.partitioned.files", "true").
    option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000").
    option("spark.cosmos.asns.partitioning.shuffle.partitions", "400").
    load()
```
---

## <a name="query-execution-with-partitioned-store"></a>Exécution de requêtes avec un magasin partitionné

Les deux configurations suivantes sont requises pour exécuter des requêtes avec prise en charge des magasins partitionnés :

* `spark.cosmos.asns.partition.keys`
* `spark.cosmos.asns.basePath`

L’exemple suivant montre comment utiliser ces configurations pour interroger le magasin partitionné ci-dessus et comment le filtrage à l’aide de la clé de partition peut utiliser le nettoyage de partition. Ce magasin partitionné est partitionné à l’aide du champ « ReadDate ».

# <a name="python"></a>[Python](#tab/python)

```python
df = spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .load()

df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
val df = spark.read.
            format("cosmos.olap").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.asns.partition.keys", "readDate String").
            option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
            load()
val df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```
---

Le filtre *ReadDate = '2021-11-01'* ci-dessus éliminera les données correspondant aux valeurs ReadDate autres que *2021-11-01* de l’analyse, pendant l’exécution.

> [!NOTE]
> Les améliorations apportées aux requêtes à l’aide d’un magasin partitionné sont applicables lorsque les requêtes sont exécutées sur les éléments suivants :
>
> * Les trames Spark créées à partir du conteneur de magasin analytique Azure Cosmos DB et
> * Les tables Spark pointent vers le conteneur du magasin analytique Azure Cosmos DB.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les documents suivants :

* [Qu’est-ce que le partitionnement personnalisé](custom-partitioning-analytical-store.md) dans Azure Synapse Link pour Azure Cosmos DB ?
* [Synapse Link pour Azure Cosmos DB](synapse-link.md)
* [Vue d’ensemble du magasin analytique Azure Cosmos DB](analytical-store-introduction.md)
* [Prise en main d’Azure Synapse Link pour Azure Cosmos DB](configure-synapse-link.md)
* [Forum aux questions sur Azure Synapse Link pour Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)
