---
title: Lier une base de données Azure Cosmos DB à votre application dans Azure Spring Cloud
description: Découvrez comment lier une base de données Azure Cosmos DB à votre application dans Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 6d9a5007ba4b5bf2c5138ab584e774d81f23bd03
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493625"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-application-in-azure-spring-cloud"></a>Lier une base de données Azure Cosmos DB à votre application dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java

Au lieu de configurer manuellement vos applications Spring Boot, vous pouvez lier automatiquement certains services Azure à vos applications à l’aide d’Azure Spring Cloud. Cet article explique comment lier votre application à une base de données Azure Cosmos DB.

Configuration requise :

* Une instance Azure Spring Cloud déployée. Pour bien démarrer, suivez notre [guide de démarrage rapide sur le déploiement d’une application via Azure CLI](./quickstart.md).
* Un compte Azure Cosmos DB avec un niveau d’autorisation minimal de contributeur

## <a name="prepare-your-java-project"></a>Préparation du projet Java

1. Ajoutez l’une des dépendances suivantes dans le fichier pom.xml de votre application. Choisissez la dépendance qui convient à votre type d’API.

    * Type d’API : Core (SQL)

      ```xml
      <dependency>
          <groupId>com.azure.spring</groupId>
          <artifactId>azure-spring-boot-starter-cosmos</artifactId>
          <version>3.6.0</version>
      </dependency>
      ```

    * Type d’API : MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Type d’API : Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Type d’API : table Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Mettez à jour l’application actuelle en exécutant `az spring-cloud app deploy` ou créez un déploiement pour ce changement en exécutant `az spring-cloud app deployment create`.

## <a name="bind-your-app-to-the-azure-cosmos-db"></a>Lier votre application à Azure Cosmos DB

#### <a name="service-binding"></a>[liaison de service](#tab/Service-Binding)
Azure Cosmos DB comprend cinq types d’API qui prennent en charge les liaisons. La procédure suivante montre comment les utiliser :

1. Crée une base de données Azure Cosmos DB. Pour obtenir de l’aide, reportez-vous au guide de démarrage rapide sur la [création d’une base de données](../cosmos-db/create-cosmosdb-resources-portal.md).

1. Prenez note du nom de votre base de données. Dans cette procédure, le nom de la base de données est **testdb**.

1. Accédez à la page du service Azure Spring Cloud dans le portail Azure. Accédez au **Tableau de bord de l’application** et sélectionnez l’application à lier à Azure Cosmos DB. Il s’agit de l’application que vous avez mise à jour ou déployée à l’étape précédente.

1. Sélectionnez **Liaison de service**, puis sélectionnez **Créer une liaison de service**. Pour remplir le formulaire, sélectionnez :
   * Le **type de liaison** **Azure Cosmos DB**
   * Le type d’API
   * Le nom de votre base de données
   * Un compte Azure Cosmos DB

    > [!NOTE]
    > Si vous utilisez Cassandra, utilisez un espace de clés pour le nom de la base de données.

1. Redémarrez l’application en sélectionnant **Redémarrer** dans la page de l’application.

1. Pour vérifier que le service est lié correctement, sélectionnez le nom de la liaison et examinez les détails. Le champ `property` doit être similaire à l’exemple suivant :

    ```properties
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)
Le script Terraform suivant montre comment configurer une application Azure Spring Cloud avec l’API MongoDB d’Azure Cosmos DB.

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_cosmosdb_account" "cosmosdb" {
  name                = "cosmosacct-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  offer_type          = "Standard"
  kind                = "MongoDB"

  consistency_policy {
    consistency_level = "Session"
  }

  geo_location {
    failover_priority = 0
    location          = azurerm_resource_group.example.location
  }
}

resource "azurerm_cosmosdb_mongo_database" "cosmosdb" {
  name                = "cosmos-${var.application_name}-001"
  resource_group_name = azurerm_cosmosdb_account.cosmosdb.resource_group_name
  account_name        = azurerm_cosmosdb_account.cosmosdb.name
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "${var.application_name}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "${var.application_name}-app"
  resource_group_name = azurerm_resource_group.example.name
  service_name        = azurerm_spring_cloud_service.example.name
  is_public           = true
  https_only          = true
}

resource "azurerm_spring_cloud_java_deployment" "example" {
  name                = "default"
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  cpu                 = 2
  memory_in_gb        = 4
  instance_count      = 2
  jvm_options         = "-XX:+PrintGC"
  runtime_version     = "Java_11"

  environment_variables = {
    "azure.cosmosdb.uri" : azurerm_cosmosdb_account.cosmosdb.connection_strings[0]
    "azure.cosmosdb.database" : azurerm_cosmosdb_mongo_database.cosmosdb.name
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu comment lier votre application dans Azure Spring Cloud à une base de données Azure Cosmos DB. Pour en savoir plus sur la liaison de services à votre application, consultez [Lier à un cache Azure Cache pour Redis](./how-to-bind-redis.md).
