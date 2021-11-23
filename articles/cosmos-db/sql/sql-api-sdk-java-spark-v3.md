---
title: Notes de publication et ressources du connecteur OLTP Azure Cosmos DB Apache Spark 3 pour l’API SQL (préversion)
description: Découvrez Azure Cosmos DB Apache Spark 3 OLTP Connector pour l’API SQL, notamment les dates de mise en production, les dates de mise hors service et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/12/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: abb10485baebc0b7379ec650fd9eeb95ebadf326
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491276"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 3 OLTP Connector pour API Core (SQL) : notes de publication et ressources
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK .NET Core v2](sql-api-sdk-dotnet-core.md)
> * [SDK .NET Change Feed v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Kit SDK Java v4](sql-api-sdk-java-v4.md)
> * [Kit SDK Java asynchrone v2](sql-api-sdk-async-java.md)
> * [SDK Java Sync v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connecteur OLTP Spark 3](sql-api-sdk-java-spark-v3.md)
> * [Connecteur OLTP Spark 2](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [API REST Resource Provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [Exécuteur en bloc – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

Le **connecteur Azure Cosmos DB OLTP Spark** fournit la prise en charge d’Apache Spark pour Azure Cosmos DB à l’aide de l’API SQL. Azure Cosmos DB est un service de base de données distribué à l’échelle mondiale qui permet aux développeurs de travailler sur des données à l’aide de diverses API standard, telles que SQL, MongoDB, Cassandra, Graph et Table.

Si vous avez des commentaires ou des idées sur l’amélioration de votre expérience, créez un problème dans notre [référentiel GitHub pour le kit de développement logiciel (SDK)](https://github.com/Azure/azure-sdk-for-java/issues/new)

## <a name="documentation-links"></a>Liens vers la documentation

* [Bien démarrer](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
* [API Catalogue](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
* [Référence des paramètres de configuration](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)

## <a name="version-compatibility"></a>Compatibilité des versions

| Connecteur     | Version de Spark minimale | Version Java minimale | Versions de Scala prises en charge | Runtimes Databricks pris en charge |
| ------------- | --------------------- | -------------------- | -----------------------  | ----------------------------- |
| 4.4.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*, 9.\*                    |
| 4.3.1         | 3.1.1                 | 8                    | 2.12                     | 8.\*, 9.\*                    |
| 4.3.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*, 9.\*                    |
| 4.2.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.1.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0-beta.3  | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0-beta.2  | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0-beta.1  | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |

## <a name="download"></a>Téléchargement

Vous pouvez utiliser la coordonnée Maven du jar pour installer automatiquement le connecteur Spark sur votre Databricks Runtime 8 à partir de Maven : `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.4.0`

Vous pouvez également intégrer le connecteur Spark Cosmos DB dans votre projet SBT :

```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.4.0"
```

Le connecteur Azure Cosmos DB est disponible dans le [référentiel central Maven](https://search.maven.org/search?q=g:com.azure.cosmos.spark).

Si vous rencontrez un bogue ou si vous souhaitez suggérer une modification de fonctionnalité, [signalez un problème](https://github.com/Azure/azure-sdk-for-java/issues/new). 

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Découvrez plus en détail [Apache Spark](https://spark.apache.org/).