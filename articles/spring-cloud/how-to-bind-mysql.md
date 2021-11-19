---
title: Comment lier une instance Azure Database pour MySQL à votre application dans Azure Spring Cloud
description: Découvrez comment lier une instance Azure Database pour MySQL à votre application dans Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 7c48b9eee1ff727cbd3d2ab7204045d962a21f44
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490342"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-application-in-azure-spring-cloud"></a>Lier une instance Azure Database pour MySQL à votre application dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java

Azure Spring Cloud vous permet de lier automatiquement certains services Azure à vos applications au lieu de configurer manuellement votre application Spring Boot. Cet article vous montre comment lier votre application à votre instance Azure Database pour MySQL.

## <a name="prerequisites"></a>Prérequis

* Une instance Azure Spring Cloud déployée
* Un compte Azure Database pour MySQL
* Azure CLI

Si vous n’avez pas d’instance Azure Spring Cloud déployée, suivez les instructions du [Guide de démarrage rapide : lancer une application dans Azure Spring Cloud à l’aide du portail Azure](./quickstart.md) pour déployer votre première application Spring Cloud.

## <a name="prepare-your-java-project"></a>Préparation du projet Java

1. Dans le fichier *pom.xml* de votre projet, ajoutez la dépendance suivante :

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```

1. Dans le fichier *application.properties*, supprimez toutes les propriétés `spring.datasource.*`.

1. Mettez à jour l’application actuelle en exécutant `az spring-cloud app deploy` ou créez un déploiement pour ce changement en exécutant `az spring-cloud app deployment create`.

## <a name="bind-your-app-to-the-azure-database-for-mysql-instance"></a>Liaison de l’application à l’instance Azure Database pour MySQL

#### <a name="service-binding"></a>[liaison de service](#tab/Service-Binding)
1. Notez le nom d’utilisateur et le mot de passe de l’administrateur de votre compte Azure Database pour MySQL.

1. Connectez-vous au serveur, créez une base de données nommée **testdb** à partir d’un client MySQL, puis créez un nouveau compte non-administrateur.

1. Dans le portail Azure, dans la page de votre service **Azure Spring Cloud**, recherchez le **Tableau de bord d’application**, puis sélectionnez l’application à lier à votre instance Azure Database pour MySQL.  Il s’agit de la même application que celle que vous avez mise à jour ou déployée à l’étape précédente.

1. Sélectionnez **Liaison de service**, puis sélectionnez le bouton **Créer une liaison de service**.

1. Remplissez le formulaire en sélectionnant **Azure MySQL** comme **Type de liaison** et en utilisant le même nom de base de données que celui que vous avez utilisé précédemment, et les mêmes nom d’utilisateur et mot de passe que ceux que vous avez notés à la première étape.

1. Redémarrez l’application. La liaison doit maintenant fonctionner.

1. Pour vérifier que la liaison de service est correcte, sélectionnez le nom de liaison et examinez ses détails. Le champ `property` doit se présenter comme ceci :

    ```properties
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

Le script Terraform suivant montre comment configurer une application Azure Spring Cloud avec Azure Database pour MySQL.

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

variable "administrator_login" {
  type        = string
  description = "The MySQL administrator login"
  default     = "myadmin"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "random_password" "password" {
  length           = 32
  special          = true
  override_special = "_%@"
}

resource "azurerm_mysql_server" "database" {
  name                = "mysql-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location

  administrator_login          = var.administrator_login
  administrator_login_password = random_password.password.result

  sku_name                          = "B_Gen5_1"
  storage_mb                        = 5120
  version                           = "5.7"
  auto_grow_enabled                 = true
  backup_retention_days             = 7
  geo_redundant_backup_enabled      = false
  infrastructure_encryption_enabled = false
  public_network_access_enabled     = true
  ssl_enforcement_enabled           = true
  ssl_minimal_tls_version_enforced  = "TLS1_2"
}

resource "azurerm_mysql_database" "database" {
  name                = "mysqldb-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  charset             = "utf8"
  collation           = "utf8_unicode_ci"
}

# This rule is to enable the 'Allow access to Azure services' checkbox
resource "azurerm_mysql_firewall_rule" "database" {
  name                = "mysqlfw-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  start_ip_address    = "0.0.0.0"
  end_ip_address      = "0.0.0.0"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "example-springcloudapp"
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
    "spring.datasource.url" : "jdbc:mysql://${azurerm_mysql_server.database.fqdn}:3306/${azurerm_mysql_database.database.name}?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC"
    "spring.datasource.username" : "${var.administrator_login}@${azurerm_mysql_server.database.name}"
    "spring.datasource.password" : random_password.password.result
    "spring.jpa.properties.hibernate.dialect" : "org.hibernate.dialect.MySQL5InnoDBDialect"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à lier une application dans Azure Spring Cloud à une instance Azure Database pour MySQL. Pour en savoir plus sur la liaison de services à une application, consultez [Lier une base de données Azure Cosmos DB à une application dans Azure Spring Cloud](./how-to-bind-cosmos.md).
