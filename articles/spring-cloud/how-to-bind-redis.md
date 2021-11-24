---
title: Lier Azure Cache pour Redis à votre application dans Azure Spring Cloud
description: Découvrez comment lier Azure Cache pour Redis à votre application dans Azure Spring Cloud.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 9f357526b64b34d7348114ce71840a5f4aafc394
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492494"
---
# <a name="bind-azure-cache-for-redis-to-your-application-in-azure-spring-cloud"></a>Lier Azure Cache pour Redis à votre application dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java

Au lieu de configurer manuellement vos applications Spring Boot, vous pouvez lier automatiquement certains services Azure à vos applications à l’aide d’Azure Spring Cloud. Cet article explique comment lier votre application à Azure Cache for Redis.

## <a name="prerequisites"></a>Prérequis

* Une instance Azure Spring Cloud déployée
* Une instance du service Cache Azure pour Redis
* L’extension Azure Spring Cloud pour Azure CLI

Si vous n’avez pas encore déployé d’instance Azure Spring Cloud, suivez les étapes décrites dans ce [guide de démarrage rapide pour déployer une application Spring Cloud](./quickstart.md).

## <a name="prepare-your-java-project"></a>Préparation du projet Java

1. Ajoutez la dépendance suivante au fichier pom.xml de votre projet :

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```

1. Supprimer les propriétés de `spring.redis.*` du fichier `application.properties`

1. Mettez à jour le déploiement actuel avec `az spring-cloud app update` ou créez un déploiement avec `az spring-cloud app deployment create`.

## <a name="bind-your-app-to-the-azure-cache-for-redis"></a>Lier votre application à Azure Cache pour Redis

#### <a name="service-binding"></a>[liaison de service](#tab/Service-Binding)
1. Accédez à la page du service Azure Spring Cloud dans le portail Azure. Accédez au **tableau de bord de l’application** et sélectionnez l’application à lier à Azure Cache for Redis. Il s’agit de l’application que vous avez mise à jour ou déployée à l’étape précédente.

1. Sélectionnez **Liaison de service**, puis sélectionnez **Créer une liaison de service**. Remplissez le formulaire en veillant à sélectionner la valeur **Azure Cache for Redis** pour le **type de liaison**, votre serveur Azure Cache for Redis, ainsi que l’option de clé **Primaire**.

1. Redémarrez l’application. La liaison doit maintenant fonctionner.

1. Pour vérifier que la liaison de service est correcte, sélectionnez le nom de la liaison et examinez ses détails. Le champ `property` doit se présenter comme ceci :

    ```properties
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

Le script Terraform suivant montre comment configurer une application Azure Spring Cloud avec Azure Cache pour Redis.

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

resource "azurerm_redis_cache" "redis" {
  name                = "redis-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  capacity            = 0
  family              = "C"
  sku_name            = "Standard"
  enable_non_ssl_port = false
  minimum_tls_version = "1.2"
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
    "spring.redis.host"     = azurerm_redis_cache.redis.hostname
    "spring.redis.password" = azurerm_redis_cache.redis.primary_access_key
    "spring.redis.port"     = "6380"
    "spring.redis.ssl"      = "true"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert comment lier votre application dans Azure Spring Cloud à Azure Cache pour Redis. Pour en savoir plus sur la liaison de services à votre application, consultez [Lier à une instance Azure Database pour MySQL](./how-to-bind-mysql.md).
