---
title: Intégrer Azure Database pour PostgreSQL avec le Connecteur de services
description: Intégrer Azure Database pour PostgreSQL dans votre application avec le Connecteur de services
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6c8cfb866da9a57c54f443558212c5d88269fe0a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096216"
---
# <a name="integrate-azure-database-for-postgresql-with-service-connector"></a>Intégrer Azure Database pour PostgreSQL avec le Connecteur de services

Cette page affiche les types d’authentification pris en charge et les types d’Azure Database pour PostgreSQL utilisant le Connecteur de services. Il se peut que vous puissiez vous connecter à Azure Database pour PostgreSQL dans d’autres langages de programmation sans utiliser le Connecteur de services. Cette page affiche également le nom et la valeur de la variable d’environnement par défaut (ou configuration du Spring Boot) que vous recevez lorsque vous créez la connexion au service. Vous pouvez en savoir plus sur la [convention d’affectation des noms des variables de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Service de calcul pris en charge

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Types d’authentification et de clients pris en charge

| Type de client | Identité managée affectée par le système | Identité managée affectée par l’utilisateur | Secret/ConnectionString | Principal de service |
| --- | --- | --- | --- | --- |
| .Net (ADO.NET) | | | ![icône Oui](./media/green-check.png) | |
| Java (JDBC) | | | ![icône Oui](./media/green-check.png) | |
| Java – Spring Boot (JDBC) | | | ![icône Oui](./media/green-check.png) | |
| Node.js (pg) | | | ![icône Oui](./media/green-check.png) | |
| Python (psycopg2) | | | ![icône Oui](./media/green-check.png) | |
| Python-Django | | | ![icône Oui](./media/green-check.png) | |
| Go (PG) | | | ![icône Oui](./media/green-check.png) | |
| PHP (natif) | | | ![icône Oui](./media/green-check.png) | |
| Ruby (ruby-pg) | | | ![icône Oui](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>Noms des variables d’environnement par défaut ou propriétés de l’application

### <a name="net-adonet"></a>.NET (ADO.NET) 

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Chaîne de connexion ADO.NET PostgreSQL | `Server={your-postgres-server-name}.postgres.database.azure.com;Database={database-name};Port=5432;Ssl Mode=Require;User Id={username}@{servername};Password=****;` |

### <a name="java-jdbc"></a>Java (JDBC)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Chaîne de connexion JDBC PostgreSQL | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require&user={username}%40{servername}l&password=****` |

### <a name="java---spring-boot-jdbc"></a>Java – Spring Boot (JDBC)

**Secret/ConnectionString**

| Propriétés de l’application | Description | Valeur d'exemple |
| --- | --- | --- |
| spring.datatsource.url | DATABASE_URL | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require` |
| spring.datatsource.username | Nom d’utilisateur de la base de données | `{username}@{servername}` |
| spring.datatsource.password | Mot de passe de la base de données | `****` |

### <a name="nodejs-pg"></a>Node.js (pg) 

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
|---------|---------|---------|
| AZURE_POSTGRESQL_HOST | URL de l’hôte de la base de données | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | Nom d’utilisateur de la base de données | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | Mot de passe de la base de données | `****` |
| AZURE_POSTGRESQL_DATABASE | Nom de la base de données | `{database-name}` |
| AZURE_POSTGRESQL_PORT | Numéro de port  | `5432` |
| AZURE_POSTGRESQL_SSL | Option SSL  | `true` |

### <a name="python-psycopg2"></a>Python (psycopg2)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Chaîne de connexion psycopg2 | `dbname={database-name} host={your-postgres-server-name}.postgres.database.azure.com port=5432 sslmode=require user={username}@{servername} password=****` |

### <a name="python-django"></a>Python-Django

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_POSTGRESQL_HOST | URL de l’hôte de la base de données | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | Nom d’utilisateur de la base de données | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | Mot de passe de la base de données | `****` |
| AZURE_POSTGRESQL_NAME | Nom de la base de données | `{database-name}` |


### <a name="go-pg"></a>Go (PG)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Chaîne de connexion Go pg | `host={your-postgres-server-name}.postgres.database.azure.com dbname={database-name} sslmode=require user={username}@{servername} password=****` |


### <a name="php-native"></a>PHP (natif)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Chaîne de connexion PHP native postgres | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=requrie user={username}@{servername} password=****` |

### <a name="ruby-ruby-pg"></a>Ruby (ruby-pg)

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Chaîne de connexion Ruby pg | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=require user={username}@{servername} password=****` |

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
