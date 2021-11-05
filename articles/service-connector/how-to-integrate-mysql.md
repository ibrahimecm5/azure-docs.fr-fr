---
title: Intégrer Azure Database pour MySQL avec Service Connector
description: Intégrer Azure Database pour MySQL à votre application avec Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce2839d614298d33f5bb1697c328b258463b6269
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096478"
---
# <a name="integrate-azure-database-for-mysql-with-service-connector"></a>Intégrer Azure Database pour MySQL avec Service Connector

Cette page affiche les types d’authentification pris en charge et les types d’Azure Database pour MySQL utilisant Service Connector. Il est possible que vous puissiez vous connecter à Azure Database pour MySQL dans d’autres langages de programmation sans utiliser Service Connector. Cette page affiche également le nom et la valeur de la variable d’environnement par défaut (ou configuration du Spring Boot) que vous recevez lorsque vous créez la connexion au service. Vous pouvez en savoir plus sur la [convention d’affectation des noms des variables de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Service de calcul pris en charge

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Types d’authentification et de clients pris en charge

| Type de client | Identité managée affectée par le système | Identité managée affectée par l’utilisateur | Secret/ConnectionString | Principal de service |
| --- | --- | --- | --- | --- |
| .Net (MySqlConnector) | | | ![icône Oui](./media/green-check.png) | |
| Java (JDBC) | | | ![icône Oui](./media/green-check.png) | |
| Java – Spring Boot (JDBC) | | | ![icône Oui](./media/green-check.png) | |
| Node.js (mysql) | | | ![icône Oui](./media/green-check.png) | |
| Python (mysql-connector-python) | | | ![icône Oui](./media/green-check.png) | |
| Python-Django | | | ![icône Oui](./media/green-check.png) | |
| Go (pilote go-sql-driver pour MySQL) | | | ![icône Oui](./media/green-check.png) | |
| PHP (mysqli) | | | ![icône Oui](./media/green-check.png) | |
| Ruby (mysql2) | | | ![icône Oui](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>Noms des variables d’environnement par défaut ou propriétés de l’application

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector) 

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | Chaîne de connexion ADO.NET MySQL | `Server={MySQLName}.mysql.database.azure.com;Database={MySQLDbName};Port=3306;SSL Mode=Required;User Id={MySQLUsername};Password={TestDbPassword}` |

### <a name="java-jdbc"></a>Java (JDBC)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | Chaîne de connexion JDBC MySQL | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required&user={MySQLUsername}&password={Uri.EscapeDataString(TestDbPassword)}` |

### <a name="java---spring-boot-jdbc"></a>Java – Spring Boot (JDBC)

**Secret/ConnectionString**

| Propriétés de l’application | Description | Valeur d'exemple |
| --- | --- | --- |
| spring.datatsource.url | URL de la base de données JDBC Spring Boot | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required` |
| spring.datatsource.username | Nom d’utilisateur de la base de données | `{MySQLUsername}@{MySQLName}` |
| spring.datatsource.password | Mot de passe de la base de données | `****` |

### <a name="nodejs-mysql"></a>Node.js (mysql) 

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
|---------|---------|---------|
| AZURE_MYSQL_HOST | URL de l’hôte de la base de données  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | Nom d’utilisateur de la base de données | `MySQLDbName` |
| AZURE_MYSQL_PASSWORD | Mot de passe de la base de données | `****` |
| AZURE_MYSQL_DATABASE | Nom de la base de données  | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PORT | Numéro de port | `3306` |
| AZURE_MYSQL_SSL | Option SSL | `true` |

### <a name="python-mysql-connector-python"></a>Python (mysql-connector-python)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_MYSQL_HOST | URL de l’hôte de la base de données  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_NAME | Nom de la base de données | `{MySQLDbName}` |
| AZURE_MYSQL_PASSWORD | Mot de passe de la base de données  | `****` |
| AZURE_MYSQL_USER | Nom d’utilisateur de la base de données  | `{MySQLUsername}@{MySQLName}` |

### <a name="python-django"></a>Python-Django

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_MYSQL_HOST | URL de l’hôte de la base de données  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | Nom d’utilisateur de la base de données | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | Mot de passe de la base de données | `****` |
| AZURE_MYSQL_NAME | Nom de la base de données | `MySQLDbName` |


### <a name="go-go-sql-driver-for-mysql"></a>Go (pilote go-sql-driver pour MySQL)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | Chaîne de connexion du pilote Go-sql-driver | `{MySQLUsername}@{MySQLName}:{Password}@tcp({ServerHost}:{Port})/{Database}?tls=true` |


### <a name="php-mysqli"></a>PHP (mysqli)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
|---------|---------|---------|
| AZURE_MYSQL_HOST | URL de l’hôte de la base de données | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | Nom d’utilisateur de la base de données | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | Mot de passe de la base de données | `****` |
| AZURE_MYSQL_DBNAME | Nom de la base de données | `{MySQLDbName}` |
| AZURE_MYSQL_PORT | Numéro de port  | `3306` |
| AZURE_MYSQL_FLAG | SSL ou autres indicateurs | `MYSQLI_CLIENT_SSL` |

### <a name="ruby-mysql2"></a>Ruby (mysql2)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
|---------|---------|---------|
| AZURE_MYSQL_HOST | URL de l’hôte de la base de données  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | Nom d’utilisateur de la base de données | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | Mot de passe de la base de données | `****` |
| AZURE_MYSQL_DATABASE | Nom de la base de données | `{MySQLDbName}` |
| AZURE_MYSQL_SSLMODE | Option SSL | `required` |

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
