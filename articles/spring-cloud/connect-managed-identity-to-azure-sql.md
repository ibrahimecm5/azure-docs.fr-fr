---
title: Utiliser une identité managée pour connecter Azure SQL à l’application Azure Spring Cloud
description: Configurez une identité managée pour connecter Azure SQL à une application Azure Spring Cloud.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: e950667e1132c8eba350a5ffcada5e5d667a1288
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485637"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Utiliser une identité managée pour connecter Azure SQL Database à une application Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java

Cet article montre comment créer une identité managée pour une application Azure Spring Cloud et comment l’utiliser pour accéder à Azure SQL Database.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) est le service de bases de données relationnelles, évolutif et intelligent, conçu pour le cloud. Il est toujours à jour, avec des fonctionnalités automatisées et alimentées par l’IA qui optimisent les performances et la durabilité. Les options de stockage Hyperscale et de calcul serverless mettent automatiquement à l’échelle les ressources à la demande, ce qui vous permet de vous concentrer sur la création de nouvelles applications sans vous soucier de la taille du stockage ou de la gestion des ressources.

## <a name="prerequisites"></a>Prérequis

* Suivez le [tutoriel sur Spring Data JPA](/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) pour approvisionner une base de données Azure SQL et la faire fonctionner avec une application Java localement.
* Suivez le [tutoriel sur les identités managées affectées par le système pour Azure Spring Cloud](./how-to-enable-system-assigned-managed-identity.md) pour approvisionner une application Azure Spring Cloud avec une identité managée activée.

## <a name="grant-permission-to-the-managed-identity"></a>Accorder l’autorisation à l’identité managée

Connectez-vous à votre serveur SQL et exécutez la requête SQL suivante :

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Ce `<MIName>` respecte la règle suivante : `<service instance name>/apps/<app name>`. Par exemple : `myspringcloud/apps/sqldemo`. Vous pouvez également interroger le MIName avec Azure CLI :

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Configurer votre application Java pour utiliser une identité managée

Ouvrez le fichier *src/main/resources/application.properties* et ajoutez `Authentication=ActiveDirectoryMSI;` à la fin de la ligne suivante. Veillez à utiliser la valeur appropriée pour la variable $AZ_DATABASE_NAME.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Créer et déployer l’application sur Azure Spring Cloud

Régénérez l’application et déployez-la sur l’application Azure Spring Cloud approvisionnée dans le deuxième point de la section Prérequis. Vous disposez maintenant d’une application Spring Boot, authentifiée par une identité managée, qui utilise JPA pour stocker et récupérer des données à partir d’une base de données Azure SQL dans Azure Spring Cloud.

## <a name="next-steps"></a>Étapes suivantes

* [Comment accéder à un objet blob de stockage avec une identité managée dans Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Activer une identité managée affectée par le système pour des applications dans Azure Spring Cloud](./how-to-enable-system-assigned-managed-identity.md)
* [En savoir plus sur les identités managées pour les ressources Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Authentifier Azure Spring Cloud auprès de Key Vault dans GitHub Actions](./github-actions-key-vault.md)
