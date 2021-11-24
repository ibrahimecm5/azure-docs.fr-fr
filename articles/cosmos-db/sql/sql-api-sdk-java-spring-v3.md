---
title: Spring Data Azure Cosmos DB v3 pour les notes de publication et les ressources de l’API SQL
description: Découvrez Spring Data Azure Cosmos DB v3 pour l’API SQL, notamment les dates de mise en production, les dates de mise hors service et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java asynchrone SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 5cf49696bb162bff92acc52db1e2ab95cee3927c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487712"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v3 pour l’API Core (SQL) : Notes de publication et ressources
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

Spring Data Azure Cosmos DB v3 pour Core (SQL) permet aux développeurs d’utiliser Azure Cosmos DB dans des applications Spring. Spring Data Azure Cosmos DB expose l’interface de données Spring pour manipuler les bases de données et les collections, travailler avec des documents et émettre des requêtes. Les API synchrones et asynchrones (réactives) sont prises en charge dans le même artefact Maven. 

> [!IMPORTANT]
> Spring Data Azure Cosmos DB est dépendant de l’infrastructure Spring Data.
> 
> Les versions azure-spring-data-cosmos 3.0.0 à 3.4.0 prennent en charge les versions 2.2 et 2.3 de Spring Data.
> 
> Les versions azure-spring-data-cosmos 3.5.0 et ultérieures prennent en charge les versions 2.4.3 et ultérieures de Spring Data.
>

[Spring Framework](https://spring.io/projects/spring-framework) est un modèle de programmation et de configuration qui rationalise le développement d’applications Java. Spring rationalise la « plomberie » des applications en injectant des dépendances. De nombreux développeurs apprécient Spring, car il rend la création et le test d’applications plus simples. [Spring Boot](https://spring.io/projects/spring-boot) étend cette prise en charge de la plomberie en vue du développement d’applications web et de microservices. [Spring Data](https://spring.io/projects/spring-data) est un modèle de programmation et une infrastructure permettant l’accès à des magasins de données tels qu’Azure Cosmos DB à partir du contexte d’une application Spring ou Spring Boot. 

Vous pouvez utiliser Spring Data Azure Cosmos DB dans vos applications [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/).

> [!IMPORTANT]  
> Ces notes de publication sont destinées à la version 3 de Spring Data Azure Cosmos DB. Les [notes de publication de la version 2 sont accessibles ici](sql-api-sdk-java-spring-v2.md). 
>
> Spring Data Azure Cosmos DB prend uniquement en charge l’API SQL.
>
> Pour plus d’informations concernant Spring Data sur d’autres API d’Azure Cosmos DB, consultez les articles suivants :
> * [Spring Data pour Apache Cassandra avec Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB avec Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
>

## <a name="get-started-fast"></a>Démarrer rapidement

  Familiarisez-vous avec Spring Data Azure Cosmos DB en suivant le [Guide de Spring Boot Starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). L'approche Spring Boot Starter est la méthode recommandée pour commencer à utiliser le connecteur Spring Data Azure Cosmos DB.

  Vous pouvez également ajouter la dépendance Spring Data Azure Cosmos DB à votre fichier `pom.xml`, comme indiqué ci-dessous :

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Contenu utile

| Contenu | Lien |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**Documentation de l’API** | [Documentation de référence sur l’API Java](/java/api/overview/azure/spring-data-cosmos-readme?view=azure-java-stable&preserve-view=true) |
|**Contribution au Kit de développement logiciel (SDK)** | [Référentiel centralisé relatif au Kit de développement logiciel (SDK) Azure pour Java sur GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Prise en main** | [Démarrage rapide : Créer une application Spring Data Azure Cosmos DB pour gérer les données de l'API SQL Azure Cosmos DB](./create-sql-api-spring-data.md) <br> [Référentiel GitHub avec code de démarrage rapide](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Exemples de code de base** | [Azure Cosmos DB : Exemples Spring Data Azure Cosmos DB pour l'API SQL](sql-api-spring-data-sdk-samples.md) <br> [Référentiel GitHub avec exemple de code](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Conseils sur les performances**| [Conseils de performances pour le kit de développement logiciel (SDK) Java v4 (applicables à Spring Data)](performance-tips-java-sdk-v4-sql.md)| 
| **Dépannage** | [Résoudre les problèmes liés au kit de développement logiciel (SDK) Java V4 (applicable à Spring Data)](troubleshoot-java-sdk-v4-sql.md) | 
| **Ateliers et laboratoires Azure Cosmos DB** |[Page d’accueil relative aux ateliers Cosmos DB](https://aka.ms/cosmosworkshop)

> [!IMPORTANT]
> * La version 3.5.0 prend en charge Spring Boot 2.4.3 et versions ultérieures.

## <a name="release-history"></a>Historique des mises en production
L’historique des versions est conservé dans le référentiel azure-sdk-for-java. Pour obtenir une liste détaillée des versions, consultez le [fichier d’historique des versions](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md).

## <a name="recommended-version"></a>Version recommandée

Il est fortement recommandé d’utiliser la version 3.10.0 et les versions ultérieures.

## <a name="additional-notes"></a>Remarques supplémentaires

* Spring Data Azure Cosmos DB prend en charge Java JDK 8 et Java JDK 11.
* Spring Data 2.3 est actuellement pris en charge, mais pas Spring Data 2.4.

## <a name="faq"></a>Questions fréquentes (FAQ)

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Apprenez-en davantage sur [Spring Framework](https://spring.io/projects/spring-framework).

Apprenez-en davantage sur [Spring Boot](https://spring.io/projects/spring-boot).

Apprenez-en davantage sur [Spring Data](https://spring.io/projects/spring-data).
