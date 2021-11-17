---
title: 'Azure Cosmos DB : API, SDK et ressource SQL Async Java'
description: Découvrez l’API et le Kit de développement logiciel (SDK) Java Async SQL, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java Async SQL d’Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: ecec077db8ca0b9b1a5285256eabb67791d461f2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116318"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Kit de développement logiciel (SDK) Java Async Azure Cosmos DB pour API SQL : Notes de publication et ressources
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK .NET Core v2](sql-api-sdk-dotnet-core.md)
> * [SDK .NET Change Feed v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Kit SDK Java v4](sql-api-sdk-java-v4.md)
> * [SDK Java Async v2](sql-api-sdk-async-java.md)
> * [SDK Java Sync v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connecteur OLTP Spark 3](sql-api-sdk-java-spark-v3.md)
> * [Connecteur OLTP Spark 2](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [API REST Resource Provider](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-query-getting-started.md)
> * [Exécuteur en bloc – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

Le Kit de développement logiciel (SDK) Java Async de l’API SQL est différent du Kit de développement logiciel (SDK) Java de l’API SQL en fournissant des opérations asynchrones avec prise en charge de la [bibliothèque Netty](https://netty.io/). Le [Kit de développement logiciel (SDK) Java de l’API SQL](sql-api-sdk-java.md) existant ne prend pas en charge les opérations asynchrones. 

> [!IMPORTANT]  
> Il ne s’agit *pas* du Kit de développement logiciel (SDK) Java pour Azure Cosmos DB le plus récent. Envisagez d’utiliser le [Kit de développement logiciel (SDK) Java v4 Azure Cosmos DB](sql-api-sdk-java-v4.md) pour votre projet. Suivez les instructions fournies dans les guides [Migrer vers le Kit de développement logiciel (SDK) Java v4 Azure Cosmos DB](migrate-java-v4-sdk.md) et [Reactor contre RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) pour la mise à niveau. 
>

> [!IMPORTANT]  
> Le 31 août 2024, le kit de développement logiciel (SDK) Java Async Azure Cosmos DB v2.x sera supprimé. Le SDK et toutes les applications qui utilisent le kit de développement logiciel (SDK) **continueront à fonctionner**. Azure Cosmos DB cessera simplement de fournir une maintenance et un support pour ce kit de développement logiciel (SDK).
> Nous vous recommandons de suivre les instructions ci-dessus pour migrer vers le kit de développement logiciel (SDK) Java Azure Cosmos DB v4.
>

| | Liens |
|---|---|
| **Téléchargement du Kit de développement logiciel (SDK)** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Documentation de l’API** |[Documentation de référence sur l’API Java](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) | 
|**Contribution au Kit de développement logiciel (SDK)** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Prise en main** | [Prise en main du Kit de développement logiciel (SDK) Java Async](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Code sample** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Conseils sur les performances**| [Fichier Readme de GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Runtime minimal pris en charge**|[JDK 8](/java/azure/jdk/) | 


## <a name="faq"></a>Questions fréquentes (FAQ)
[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
