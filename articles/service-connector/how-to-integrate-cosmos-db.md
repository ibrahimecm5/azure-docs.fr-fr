---
title: Intégrer Azure Cosmos DB à l’aide de Service Connector
description: Intégrer Azure Cosmos DB à votre application à l’aide de Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 8bdd4a23ac0736f2ff4ddf3095f0d77b2af066ce
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373516"
---
# <a name="integrate-azure-cosmos-db-with-service-connector"></a>Intégrer Azure Cosmos DB à l’aide de Service Connector

Cette page affiche les types d’authentification et types de clients pris en charge par Azure Cosmos DB à l’aide de Service Connector. Vous pouvez toujours vous connecter à Azure Cosmos DB dans d’autres langages de programmation sans utiliser Service Connector. Cette page affiche également le nom et la valeur de la variable d’environnement par défaut (ou configuration du Spring Boot) que vous recevez lorsque vous créez la connexion au service. Vous pouvez en savoir plus sur la [convention d’affectation des noms des variables de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Service de calcul pris en charge

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Types d’authentification et de clients pris en charge

| Type de client | Identité managée affectée par le système | Identité managée affectée par l’utilisateur | Secret/ConnectionString | Principal de service |
| --- | --- | --- | --- | --- |
| .NET | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Java  | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Java - Spring Boot | | | ![icône Oui](./media/green-check.png) | |
| Node.js | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Go | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>Noms des variables d’environnement par défaut ou propriétés de l’application

### <a name="dotnet-java-nodejs-and-go"></a>dotnet, Java, NodeJS et Go

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_COSMOS_CONNECTIONSTRING | Chaîne de connexion MongoDB dans Cosmos DB | `mongodb://{mango-db-admin-user}:{********}@{mango-db-server}.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@{mango-db-server}@` |

**Identité managée affectée par le système**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | URL permettant d’accéder à la chaîne de connexion | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | Étendue de votre identité managée | `https://management.azure.com/.default` |
| AZURE_COSMOS_RESOURCEENDPOINT | Votre point de terminaison de ressource| `https://{your-database-server}.documents.azure.com:443/` |

**Identité managée affectée par l’utilisateur**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | URL permettant d’accéder à la chaîne de connexion | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | Étendue de votre identité managée | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | ID de votre clé secrète client | `{client-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | Votre ID d’abonnement | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | Votre point de terminaison de ressource| `https://{your-database-server}.documents.azure.com:443/` |

**Principal du service**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | URL permettant d’accéder à la chaîne de connexion | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | Étendue de votre identité managée | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | ID de votre clé secrète client | `{client-id}` |
| AZURE_COSMOS_CLIENTSECRET | Secret de votre clé secrète client | `{client-secret}` |
| AZURE_COSMOS_TENANTID | Votre ID d’abonné | `{tenant-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | Votre ID d’abonnement | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | Votre point de terminaison de ressource| `https://{your-database-server}.documents.azure.com:443/` |

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
